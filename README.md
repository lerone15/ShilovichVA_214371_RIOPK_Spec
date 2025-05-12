# **Программное средство управления номерным фондом гостиницы**

Данный проект представляет собой программное средство для мониторинга KPI сотрудников

Клиент: 
Сервер: 

---

## **Содержание**

1. [Архитектура](#Архитектура)
	1. [C4-модель](#C4-модель)
	2. [Схема данных](#Схема_данных)
2. [Функциональные возможности](#Функциональные_возможности)
	1. [Диаграмма вариантов использования](#Диаграмма_вариантов_использования)
	2. [User-flow диаграммы](#User-flow_диаграммы)
3. [Детали реализации](#Детали_реализации)
	1. [UML-диаграммы](#UML-диаграммы)
	2. [Спецификация API](#Спецификация_API)
	3. [Безопасность](#Безопасность)
	4. [Оценка качества кода](#Оценка_качества_кода)
4. [Тестирование](#Тестирование)
5. [Установка и  запуск](#installation)
6. [Лицензия](#Лицензия)
7. [Контакты](#Контакты)

---
## **Архитектура**

### C4-модель

Контекстная диаграмма С4 представлена на рисунке 1.2.

 ![image](https://github.com/user-attachments/assets/bcbb7590-e94d-4467-93ff-2546d8facf0d)

Рисунок 1.2 – Контекстная диаграмма С4

Контейнерная диаграмма С4 представлена на рисунке 1.3.

 ![image](https://github.com/user-attachments/assets/0074c8b6-6280-4833-9f8e-04ab59d147bb)

Рисунок 1.3 – Контейнерная диаграмма С4

На контейнерной диаграмме видно, что программное средство состоит из клиентской части, серверной части и базы данных. Общение между клиентом и сервером происходит с помощью API запросов.
Компонентная диаграмма представлена на рисунке 1.4.

![image](https://github.com/user-attachments/assets/514db16d-c23e-4139-a190-879f22b68c4a)

Рисунок 1.4 – Компонентная диаграмма 

На компонентной диаграмме отражены компоненты, которые используются в программном средстве: 
	авторизация;
	фидбек;
	KPI;
	API.

### Схема данных

Схема данных представлена на рисунке 4.1.

![image](https://github.com/user-attachments/assets/2de15d5b-e278-45a3-adb7-27bb5a4e995e)

Рисунок 4.1 – Схема базы данных

---

## **Функциональные возможности**

### Диаграмма вариантов использования

Диаграмма вариантов использования:

![image](https://github.com/user-attachments/assets/0f7ff53e-4f90-40c2-983a-22288a5ef7b9)


### User-flow диаграммы

User-flow диаграмма для администратора:

![image](https://github.com/user-attachments/assets/f8400a6c-869d-415b-9110-8802c6e38934)

---

## **Детали реализации**

### UML-диаграммы

Диаграммы классов представлена на рисунке 5.1.

![image](https://github.com/user-attachments/assets/646a20e8-09a4-4d21-88d8-081415d24de6)

Рисунок 5.1 – Диаграмма классов 

Диаграмма компонентов системы представлена на рисунке 5.2.

![image](https://github.com/user-attachments/assets/4592dcc9-45d9-43b2-bc05-e29e70c1dd3b)

Рисунок 5.2 – Диаграмма компонентов системы

Диаграмма последовательности представлена на рисунке 5.3.

![image](https://github.com/user-attachments/assets/1a37be08-25ba-4a21-a2b0-2f818bb09081)

Рисунок 5.3 – Диаграмма последовательности


### Спецификация API

Документация к разработанному API доступна в интерактивном режиме при запущенном сервере при помощи интегрированного инструмента Swagger UI по адресу: http://localhost:8081/swagger-ui/index.html.
Документация  представлена на рисунках 6.1 и 6.2.

 ![image](https://github.com/user-attachments/assets/3965af64-3e5c-4ced-81a9-962d29443216)

Рисунок 6.1 – Доступные методы 
![image](https://github.com/user-attachments/assets/588d35a2-9e70-4655-a517-45429bef5dc7)

Рисунок 6.2 – Пример работы метода


### Безопасность

Процесс аутентификации происходит по следующему алгоритму:
1	Клиент отправляет POST-запрос с username и password в теле запроса.
2	Сервер ищет пользователя по username в базе данных.
3	Если пользователь найден, проверяется соответствие пароля.
4	При успешной проверке генерируется случайный UUID-токен и возвращается клиенту.
Код реализации метода авторизации:
import jwt from 'jsonwebtoken';

export const login = async (req, res) => {
  try {
    const { email, password } = req.body;
    const user = await UserService.getByEmail(email);
    
    if (!user || !bcrypt.compareSync(password, user.password_hash)) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    const token = jwt.sign(
      { sub: user.id, role: user.role },
      process.env.JWT_SECRET,
      { expiresIn: '1h' }
    );

    res.json({ token });
  } catch (error) {
    res.status(500).json({ error: 'Login failed' });
  }
};
Механизмы безопасности, использованные в программном средстве:
	хеширование паролей;
	использование JWT токенов.



### Оценка качества кода

Метрика	Макс. балл	Набрано баллов	% выполнения
Error Rate: 40%
Memory Leaks:	46.6%
Event Loop Latency: 46.6%
Connection Leaks: 46.6%


---

## **Тестирование**

Для проверки работоспособности программного средства были разработаны следующие тесты:

Unit-тест для проверки авторизации:

// backend/tests/unit/auth.test.js
const request = require('supertest');
const app = require('../../index'); 
const mysql = require('mysql2/promise');

jest.mock('mysql2/promise', () => ({
  createConnection: jest.fn(),
  createPool: jest.fn()
}));

describe('Auth Controller', () => {
  beforeEach(() => {
    mysql.createConnection.mockClear();
  });

  test('Успешная авторизация', async () => {
    // Мокируем ответ БД
    mysql.createConnection.mockImplementation(() => ({
      execute: jest.fn().mockResolvedValue([[{
        username: 'test',
        password: '123',
        role: 'USER'
      }]])
    }));

    const response = await request(app)
      .post('/login')
      .send({ username: 'test', password: '123' });

    expect(response.status).toBe(200);
    expect(response.body).toHaveProperty('user', 'test');
  });

  test('Неверный пароль', async () => {
    mysql.createConnection.mockImplementation(() => ({
      execute: jest.fn().mockResolvedValue([[{
        username: 'test',
        password: 'correct',
        role: 'USER'
      }]])
    }));

    const response = await request(app)
      .post('/login')
      .send({ username: 'test', password: 'wrong' });

    expect(response.status).toBe(401);
  });
});

Интеграционный тест:

// backend/tests/integration/auth.test.js
const request = require('supertest');
const app = require('../../index');
const mysql = require('mysql2/promise');

describe('Auth API Integration Tests', () => {
  let connection;
  let testUsername;

  beforeAll(async () => {
    // Создаем отдельное соединение (не через пул)
    connection = await mysql.createConnection({
      host: process.env.DB_HOST,
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME,
      multipleStatements: true
    });

    // Очистка данных
    await connection.query(`
      SET FOREIGN_KEY_CHECKS = 0;
      DELETE FROM feedback;
      DELETE FROM performance_reviews;
      DELETE FROM goals;
      DELETE FROM users;
      SET FOREIGN_KEY_CHECKS = 1;
    `);

    // Создаем тестового пользователя
    testUsername = `testuser_${Date.now()}`;
    await connection.query(
      'INSERT INTO users (username, password, role) VALUES (?, ?, ?)',
      [testUsername, 'testpass', 'USER']
    );
  });

  afterAll(async () => {
    await connection.end();
  });

  test('POST /login успешная авторизация', async () => {
    const response = await request(app)
      .post('/login')
      .send({ 
        username: testUsername,
        password: 'testpass'
      });
    
    expect(response.status).toBe(200);
    expect(response.body).toMatchObject({
      message: 'Login successful',
      user: testUsername,
      role: 'USER'
    });
  });
});

Все юнит- и интеграционные тесты были пройдены программным средством, о чем свидетельствуют рисунки 8.1-8.2.

 ![image](https://github.com/user-attachments/assets/5726d241-d279-4a32-bbd3-5198768fc6ad)

Рисунок 8.1 – Результат выполнения unit-теста

 ![image](https://github.com/user-attachments/assets/0b8f5991-f289-423f-9a75-a41798d3cf52)

Рисунок 8.2 – Результат выполнения интеграционного теста

Все ожидаемые результаты соответствуют фактическим результатам проведения тестовых сценариев. Программное средство работает исправно и предоставляет все заявленные функции.


---

## **Установка и  запуск**

Для корректной работы системы необходимо обеспечить следующие минимальные требования:
	операционная система: Windows 10/11 (x64);
	Docker Desktop версии 4.12.0 или новее;
	оперативная память: не менее 8 ГБ;
	процессор с поддержкой виртуализации;
	свободное дисковое пространство не менее 5 ГБ (для образов Docker и работы приложения).
Дополнительно: сетевые порты 3000, 8080-8081 должны быть свободны.
Пошаговая процедура развертывания:
1	Установить Docker Desktop, запустить и дождаться его полной инициализации.
2	Распаковать архив с проектом в выбранную директорию.
3	Проект запускается через командную строку с помощью файла docker-compose.yml. Необходимо перейти в корневую папку проекта: и последовательно выполнить команды:

cd EployeePerformance
docker-compose up -d 

4	Убедиться, что все контейнеры запущены:

docker ps

В выводе должны отображаться 3 сервиса: frontend, backend, mysql.
5	Открыть в браузере адрес Фронтенд: http://localhost:3000
6	Бэкенд (тест): http://localhost:3001



---

## **Лицензия**

Этот проект лицензирован по лицензии MIT - подробности представлены в файле [LICENSE]

---

## **Контакты**

Автор: lerasamecz60@gmail.com
