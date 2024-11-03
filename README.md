## <u>Модули</u>

<br>

**1. QuadraticEquation**

**Описание:** QuadraticEquation — это веб-сервис на основе Jakarta EE, предназначенный для решения квадратных уравнений. 

Он предоставляет SOAP API, с помощью которого можно передавать коэффици-енты уравнения (a, b, c) и получать его решения (корни).

Проект разворачивается в контейнере сервлетов Apache Tomcat и предлагает интерфейс для тестирование через Postman.
 
**Структура проекта**

1.	QuadraticEquationService (интерфейс):
   
o	Определяет метод solveQuadraticEquation, который принимает коэффициенты уравнения (a, b, c) и возвращает объект SolveResponse.

o	Метод аннотирован как @WebMethod, это нужно чтобы сделать его доступным через SOAP.

2.	QuadraticEquationServiceImpl (класс):
   
o	Реализует интерфейс QuadraticEquationService и содержит логику решения квадратного уравнения.

o	Вычисляет дискриминант и в зависимости от его значения возвращает одно или два решения, либо бросает исключение DiscriminantException, если дискриминант отрицательный.

3.	DiscriminantException (класс):
   
o	Кастомное исключение, которое выбрасывается, если дискриминант отрицательный и уравнение не имеет действительных корней.

o	Содержит информацию об ошибке и объект SolveResponse.

4.	SolveResponse (класс):
   
o	Используется для представления ответа сервиса.

o	Содержит поля для формулы уравнения, значения дискриминанта (D) и двух возможных корней (x1 и x2).

5.	QuadraticEquationServicePublisher (класс):
    
o	Предназначен для локального запуска сервиса без использо-вания сервлета.

o	Запускает веб-сервис по указанному URL (например, http://localhost:8080/QuadraticEquationService).

6.	QuadraticEquationServiceServlet (класс):
    
o	Реализует HttpServlet и используется для развертывания сервиса в контейнере сервлетов Tomcat.

o	Инициализирует и публикует веб-сервис при запуске приложения и завершает его при остановке.

7.	web.xml:
    
o	Конфигурационный файл для развертывания в Tomcat, указывающий, какой сервлет использовать для обработки за-просов на определенном URL (/QuadraticEquationService/*).

8.	pom.xml:
    
o	Файл Maven, содержащий информацию о зависимостях, необходимых для работы с Jakarta EE, JAX-WS и другими библиотеками, а также настройки компиляции.
 
**Шаги по развертыванию и запуску проекта через Tomcat**

1.	Сборка проекта:
   
o	Выполните команду Maven для сборки проекта:

mvn clean install

Это создаст .war файл, который можно будет развернуть в Tomcat.

2.	Развертывание в Tomcat:
   
•	Скопируйте сгенерированный .war файл (QuadraticEquation.war) в папку webapps Tomcat.

•	Запустите Tomcat через bin/startup.sh (для Unix) или bin/startup.bat (для Windows).

•	Tomcat автоматически развернет .war файл, и сервис будет доступен по адресу:

•	http://localhost:8083/QuadraticEquationService/QuadraticEquationService

Если всё прошло успешно, откроется страница с информацией о развернутом веб-сервисе.

**Тестирование с помощью Postman**

1.	Тестирование с использованием Postman:
   
•	Создайте новый запрос, выберите тип POST, и укажите URL:

http://localhost:8083/QuadraticEquationService/QuadraticEquationService

•  Перейдите на вкладку Body, выберите "raw" и установите тип XML.

•  Вставьте SOAP-запрос:

<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"

                 xmlns:org="http://example.org/">
                 
   <soapenv:Header/>
   
   <soapenv:Body>
   
      <org:solveQuadraticEquation>
      
         <org:a>1</org:a>
         
         <org:b>-3</org:b>
         
         <org:c>2</org:c>
         
      </org:solveQuadraticEquation>
      
   </soapenv:Body>
   
</soapenv:Envelope>

<br>

Нажмите "Send" и проверьте ответ сервиса:

<br>

<?xml version='1.0' encoding='UTF-8'?>

<S:Envelope xmlns:S="http://schemas.xmlsoap.org/soap/envelope/">

    <S:Body>
    
        <ns2:solveQuadraticEquationResponse xmlns:ns2="http://example.org/">
        
            <return>
            
                <d>1.0</d>
                
                <formula>1.0x^2 + -3.0x + 2.0 = 0</formula>
                
                <x1>2.0</x1>
                
                <x2>1.0</x2>
                
            </return>
            
        </ns2:solveQuadraticEquationResponse>
        
    </S:Body>
    
</S:Envelope>

<br>

**2. SoapClient**

**Описание проекта**

Данный проект представляет собой SOAP-клиент для решения квадратных уравнений.

**Установка и настройка**

Перед началом работы убедитесь, что сервер QuadraticEquationService, который генерирует WSDL-файл, запущен. Проверьте, что вы можете получить доступ к WSDL-файлу по следующему URL:

http://localhost:8083/QuadraticEquationService/QuadraticEquationService?wsdl


**Генерация классов из WSDL**

Для начала необходимо сгенерировать необходимые классы из WSDL-файла. Для этого выполните следующую команду:

wsimport -keep -s src/main/java -p org.example.soapclient http://localhost:8083/QuadraticEquationService/QuadraticEquationService?wsdl

После выполнения этой команды в папке generated-sources будет создана папка wsimport со всеми необходимыми классами. 


**Использование Maven для автоматической генерации классов**

Можно также выполнить подключение к WSDL через файл pom.xml. В pom.xml файл добавьте следующий код в секцию <build>:

<build>
 
    <plugins>
    
        <plugin>
        
            <groupId>org.codehaus.mojo</groupId>
            
            <artifactId>jaxws-maven-plugin</artifactId>
            
            <version>2.6</version>
            
            <executions>
            
                <execution>
                
                    <goals>
                    
                        <goal>wsimport</goal>
                        
                    </goals>
                    
                    <configuration>
                    
                        <wsdlUrls>
                        
                            <wsdlUrl>http://localhost:8083/QuadraticEquationService/QuadraticEquationService?wsdl</wsdlUrl>
                            
                        </wsdlUrls>
                        
                        <packageName>org.example.soapclient</packageName>
                        
                    </configuration>
                    
                </execution>
                
            </executions>
            
        </plugin>
        
    </plugins>
    
</build>

**Настройка приложения**

spring:

  application:
  
    name: soap-client
    
logging:

  level:
  
    org.springframework.ws.client.core.WebServiceTemplate: DEBUG
    
server:

  port: 8081


**Сборка и запуск проекта**

Соберите проект, используя следующую команду:

mvn clean install

После успешной сборки запустите приложение:

mvn spring-boot:run


**Проверка работы через Postman**

Убедитесь, что сервер, предоставляющий WSDL, запущен. Теперь вы можете протестировать ваше приложение через Postman, отправив GET-запрос по следующему URL:

http://localhost:8081/api/calc?a=1&b=-3&c=2

При успешном выполнении запроса вы получите ответ в формате JSON:

{ "d": 1.0, "formula": "1.0x^2 + -3.0x + 2.0 =0", "x1": 2.0, "x2": 1.0 }
