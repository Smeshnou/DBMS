## Функциональные требования к системе управления библиотекой

### Обязательные требования:

### 1. Авторизация/аутентификация пользователя
- Регистрация новых пользователей
- Вход в систему по логину/паролю
- Восстановление пароля
- Выход из системы

### 2. Управление пользователями (CRUD)
- Создание новых пользователей
- Просмотр списка пользователей
- Редактирование данных пользователей
- Удаление пользователей
- Поиск пользователей по различным критериям

### 3. Система ролей
- Администратор - полный доступ ко всем функциям
- Библиотекарь - управление книгами, выдача/прием
- Читатель - просмотр каталога, бронирование книг

### 4. Журналирование действий пользователя
- Фиксация всех значимых действий в системе
- Хранение даты, времени, типа действия и пользователя
- Просмотр логов действий

### 5. Управление книгами
- Добавление новых книг в каталог
- Редактирование информации о книгах
- Удаление книг из каталога
- Поиск книг по различным критериям

### 6. Управление авторами и издательствами
- Ведение справочника авторов
- Ведение справочника издательств
- Связь книг с авторами и издательствами

### 7. Выдача и возврат книг
- Оформление выдачи книг читателям
- Фиксация сроков возврата
- Обработка возврата книг
- Учет просрочек

### 8. Бронирование книг
- Бронирование книг читателями
- Очередь на популярные книги
- Уведомления о доступности

### 9. Управление жанрами и категориями
- Классификация книг по жанрам
- Система категорий для организации каталога

### 10. Отчетность и статистика
- Статистика популярности книг
- Отчеты по активности читателей
- Анализ посещаемости библиотеки

## Описание сущностей базы данных

### 1. Users
- Хранит информацию о всех пользователях системы

Users (

    user_id INT PRIMARY KEY AUTO_INCREMENT,<br>
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    role_id INT NOT NULL,
    FOREIGN KEY (role_id) REFERENCES Roles(role_id)
)


### 2. Roles
- Определяет права доступа пользователей

Roles (
    role_id INT PRIMARY KEY AUTO_INCREMENT,
    role_name VARCHAR(30) UNIQUE NOT NULL
)

### 3. UserActivityLog
- Фиксирует все действия пользователей в системе

UserActivityLog (
    log_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    action_description TEXT,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
)

### 4. Books
- Основная информация о книгах

Books (
    book_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR,
    page_count INT,
    publisher_id INT,
    FOREIGN KEY (publisher_id) REFERENCES Publishers(publisher_id)
)

### 5. Authors
- Информация об авторах книг

Authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL
)

### 6. Publishers
- Сведения об издательствах

Publishers (
    publisher_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) UNIQUE NOT NULL
)

### 7. Genres
- Категории жанров литературы

Genres (
    genre_id INT PRIMARY KEY AUTO_INCREMENT,
    genre_name VARCHAR(50) UNIQUE NOT NULL
)

### 8. BookCopies
- Конкретные физические экземпляры книг

BookCopies (
    copy_id INT PRIMARY KEY AUTO_INCREMENT,
    book_id INT NOT NULL,
    is_available BOOLEAN DEFAULT TRUE,
    FOREIGN KEY (book_id) REFERENCES Books(book_id)
)

### 9. Loans
- Информация о выданных книгах

Loans (
    loan_id INT PRIMARY KEY AUTO_INCREMENT,
    copy_id INT NOT NULL,
    user_id INT NOT NULL,
    loan_date DATETIME DEFAULT CURRENT_TIMESTAMP,
    due_date DATE NOT NULL,
    return_date DATETIME,
    status ENUM('active', 'returned', 'overdue') DEFAULT 'active',
    FOREIGN KEY (copy_id) REFERENCES BookCopies(copy_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
)

### 10. Reservations
- Записи о забронированных книгах

Reservations (
    reservation_id INT PRIMARY KEY AUTO_INCREMENT,
    book_id INT NOT NULL,
    user_id INT NOT NULL,
    reservation_date DATETIME NOT NULL,
    status ENUM('active', 'returned', 'cancelled') DEFAULT 'active',
    FOREIGN KEY (book_id) REFERENCES Books(book_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
)

## Связующие таблицы

### 1. BookAuthors 

BookAuthors (
    book_id INT,
    author_id INT,
    PRIMARY KEY (book_id, author_id),
    FOREIGN KEY (book_id) REFERENCES Books(book_id),
    FOREIGN KEY (author_id) REFERENCES Authors(author_id)
)

### 2. BookGenres 

BookGenres (
    book_id INT,
    genre_id INT,
    PRIMARY KEY (book_id, genre_id),
    FOREIGN KEY (book_id) REFERENCES Books(book_id),
    FOREIGN KEY (genre_id) REFERENCES Genres(genre_id)
)
