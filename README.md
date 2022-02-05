# Репортинг (AQA_Exercise_4.1-2)
## Домашнее задание по курсу "Автоматизированное тестирование"
## Тема: «4.1. Репортинг», задание №2: «Report Portal»
1. Развертывание Report Portal на базе Docker
1. Интеграция Report Portal в существующий проект на базе JUnit5
### Предварительные требования
1. Получить доступ к удаленному серверу
1. На удаленном сервере должны быть установлены и доступны:
	- GIT
	- Docker	
	- Bash
1. На компьютере пользователя должна быть установлена:
	- Intellij IDEA
### Инструкция по интеграции Report Portal в проект на базе Gradle
1. В древе проекта перейти по адресу
	```
	src -> test -> resources
	```
1. Создать цепочку катологов META-INF/services
1. В конечный католог (services) поместить файл org.junit.jupiter.api.extension.Extension, итоговый результат будет таким
	```
	/src/test/resources/META-INF/services/org.junit.jupiter.api.extension.Extension
	```
1. Минимально в файле должна быть строка
	```
	com.epam.reportportal.junit5.ReportPortalExtension
	```
1. Создать файл reportportal.properties по адресу /src/test/resources
	- далее необходимо авторизоваться в системе Report Portal (как это сделать описано в разделе "Установка и запуск" пункты 6 и 7)
	- далее кликнуть на иконку пользователя (слева и внизу) и выбрать Profile
	- в разделе CONFIGURATION EXAMPLES будут данные для файла reportportal.properties, пример
	```
	REQUIRED
	rp.endpoint = https://demo.reportportal.io
	rp.uuid = 55466cae-8588-452c-9400-9ce4c5960e98
	rp.launch = default_TEST_EXAMPLE
	rp.project = user
	```	
1. В build.gradle прописать:
	```
	...
	dependencies {
		...

		testImplementation 'com.epam.reportportal:agent-java-junit5:5.1.1'
		testImplementation 'com.epam.reportportal:logger-java-log4j:5.1.4'
		testImplementation 'org.apache.logging.log4j:log4j-api:2.17.1'
		testImplementation 'org.apache.logging.log4j:log4j-core:2.17.1'

		...
	}
	
	test {
	...
    testLogging.showStandardStreams = true
    systemProperty 'junit.jupiter.extensions.autodetection.enabled', true
	}
	```
### Установка и запуск
1. Залогиниться на удаленный сервер
1. Создать рабочую папку, например Report_Portal командой
	```
	mrdir Report_Portal
	```
1. Перейти в созданный каталог командой
	```
	cd Report_Portal
	```
1. Загрузить файл docker-compose.yml командой
	```
	curl -LO https://raw.githubusercontent.com/Lognestix/AQA_Exercise_4.1-2/master/docker-compose.yml
	```
1. Развернуть Report Portal командой:
	```
	docker-compose -p reportportal up -d --force-recreate
	```
	- -p reportportal добавляет префикс проекта «reportportal» ко всем контейнерам
	- up создает и запускает контейнеры
	- -d режим демона
	- --force-recreate пересоздает контейнеры, если они есть	
1. Перейти на страницу http://ip_server:8080 , где ip_server - ip удаленного сервера
1. Далее либо создается свой пользователь, либо используется уже созданный.
	1. Для создания пользователя:
		- Необходимо залогиниться с правами администратора (Login: superadmin/Password: erebus)
		- Перейти во вкладках по указанному пути:
			```
			Administrative -> My Test Project -> Members -> Add user
			```
		- Ввести имя и пароль для нового пользователя
		- Перелогиниться под только что созданным пользователем
	1. Для использования ранее созданного пользователя:
		- Залогиниться (Login: default/Password: 1q2w3e)
1. Склонировать проект на свой компьютер
	- открыть терминал
	- ввести команду 
		```
		git clone https://github.com/Lognestix/AQA_Exercise_4.1-2
		```
1. Открыть склонированный проект в Intellij IDEA
1. В Intellij IDEA перейти во вкладку Terminal (Alt+F12) и запустить SUT командой
	```
	java -jar artifacts/app-card-delivery.jar
	```
1. В Intellij IDEA во вкладке Terminal открыть еще одну сессию и ввести команду
	```
	./gradlew clean test
	```
1. Перейтик к странице с залогиненым пользователем ReportPortal (например [demoReportPortal](https://demo.reportportal.io) login/pass: default/1q2w3e)
	- кликнуть на вкладку Launches (слева), появится название launches эквивалентное указанному в файле reportportal.properties
	- кликнуть на название launches, появится список тестов
	- кликнуть на любой из них (список тестов), отобразятся соотвествующие отчеты и логи