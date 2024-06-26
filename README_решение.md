## 1. Вопросы для разогрева

1. Расскажите, с какими задачами в направлении безопасной разработки вы сталкивались? 

2. Если вам приходилось проводить security code review или моделирование угроз, расскажите, как это было? 
3. Если у вас был опыт поиска уязвимостей, расскажите, как это было? 
4. Почему вы хотите участвовать в стажировке?

## 2. Security code review

### Часть 1. Security code review: GO

Уязвимости:

1. SQL инъекция: Уязвимость, связанная с формированием SQL-запроса через конкатенацию строк. В строке 27 формируется SQL запрос с помощью fmt.Sprintf без использования параметризированных запросов.
2. Отсутствие валидации входных данных: Не происходит достаточной валидации параметра, переданного в запросе (строка 21).
3. Хранение пароля в исходном коде: Пароль к базе данных хранится в исходном коде (строка 15).

Строки с уязвимостями:

- 32: отсутствие валидации параметра searchQuery.

- 38: SQL-инъекция при использовании fmt.Sprintf для формирования SQL-запроса.

Последствия эксплуатации уязвимостей:

1. SQL инъекция может привести к выполнению зловредного SQL-кода в базе данных, что может привести к утечке данных, удалению или изменению записей.
2. Отсутствие валидации входных данных предоставляет злоумышленнику возможность запуска различных атак, таких как XSS или CSRF.
3. Хранение пароля в исходном коде обеспечивает доступ к базе данных злоумышленникам, если они получат доступ к коду.

Способы исправления уязвимостей:

1. SQL инъекция: Использовать параметризованные запросы вместо конкатенации строк для формирования запросов (например, db.Query("SELECT * FROM products WHERE name LIKE ?", "%"+searchQuery+"%")).
2. Валидация входных данных: Проводить валидацию и очистку пользовательского ввода перед использованием в SQL-запросах.
3. Хранение паролей: Хранить конфиденциальные данные, такие как пароли, в переменных среды или использовать управление секретами, такое как Vault.

Выбор наилучшего способа исправления уязвимости зависит от конкретной ситуации. Однако, использование параметризованных запросов для предотвращения SQL инъекций является более предпочтительным и безопасным подходом, так как это делает запросы невосприимчивыми к внедрению кода.


В примере №2.1 есть следующая уязвимость:

1. Уязвимость: XSS (межсайтовый скриптинг) - в строке output = Template('Hello ' + name + '! Your age is ' + age + '.').render() пользовательский ввод (name и age) вставляется напрямую в шаблон без должной обработки, что позволяет злоумышленнику внедрить вредоносный JavaScript код и выполнить его на стороне клиента.

Возможные последствия: атаки на пользователей, кража данных, перехват сессии, изменение содержимого страницы и другие опасные действия.

Способ исправления: для предотвращения XSS рекомендуется использовать функцию escape() перед вставкой пользовательского ввода в шаблон. Например, output = Template('Hello ' + escape(name) + '! Your age is ' + escape(age) + '.').render(). Также можно использовать Jinja2 фильтры безопасности для автоматической фильтрации вредоносного кода.
В примере №2.2 есть следующая уязвимость:

1. Уязвимость: Инъекция команд - в строке output = subprocess.check_output(cmd, shell=True, text=True) переменная cmd, которая формируется путем конкатенации пользовательского ввода (hostname) с командой nslookup, подвержена инъекции команд, что позволяет злоумышленнику выполнить произвольные команды на сервере.

Возможные последствия: утечка конфиденциальных данных, нежелательное выполнение системных команд, компрометация сервера и другие серьезные последствия.

Способ исправления: для предотвращения инъекции команд рекомендуется использовать безопасные методы формирования команд, такие как использование списка аргументов subprocess.check_output(["nslookup", hostname], text=True), вместо конкатенации строк. Также рекомендуется производить валидацию пользовательского ввода и фильтрацию опасных символов.
Выбор лучшего способа исправления уязвимостей зависит от конкретного контекста и требований проекта. Однако, в целом, использование безопасных методов формирования строк и фильтрации пользовательского ввода является хорошей практикой.