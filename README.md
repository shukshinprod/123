using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;

struct Movie
{
    public string Title; // название фильма
    public string Country; // Страна производства
    public DateTime ShowTime; // Дата и время показа фильма
    public int TotalSeats; // Общее количество мест
    public int AvailableSeats; // Количество доступных мест
    public decimal TicketPrice; // Стоимость билета
    public int AgeRestrictions; // Возрастные ограничения
}

class Program
{
    static List<Movie> movies = new List<Movie>() {
            new Movie { Title = "IBM", Country = "Россия", ShowTime = DateTime.Now, TotalSeats = 100, AvailableSeats = 80, TicketPrice = 100, AgeRestrictions = 18 },
            new Movie { Title = "Криптография", Country = "Великобритания", ShowTime = DateTime.Now.AddMinutes(20), TotalSeats = 150, AvailableSeats = 120, TicketPrice = 120, AgeRestrictions = 12 },
            new Movie { Title = "Социальная сеть", Country = "Белоруссия", ShowTime = DateTime.Now.AddMinutes(-20), TotalSeats = 200, AvailableSeats = 150, TicketPrice = 80, AgeRestrictions = 16 },
            new Movie { Title = "Игра в имитацию", Country = "США", ShowTime = DateTime.Now.AddHours(3), TotalSeats = 120, AvailableSeats = 100, TicketPrice = 90, AgeRestrictions = 18 },
            new Movie { Title = "Джобс", Country = "Австралия", ShowTime = DateTime.Now.AddHours(-3), TotalSeats = 180, AvailableSeats = 150, TicketPrice = 110, AgeRestrictions = 12 }
 }; // Список всех фильмов

    static void Main()
    {
        string filePath = ".xml";

        if (File.Exists(filePath))
        {
            Console.WriteLine("Файл существует.");
        }
        else
        {
            Console.WriteLine("Файл не существует.");
            while (true) // Вывод возможных действий
            {
                Console.WriteLine("Выберите действие:");
                Console.WriteLine("1. Создание новой записи");
                Console.WriteLine("2. Вывод списка на экран");
                Console.WriteLine("3. Поиск записей");
                Console.WriteLine("4. Удаление записи");
                Console.WriteLine("5. Выход");
                Console.Write("Введите номер действия: ");

                int choice; // Ввод действия и проверка
                if (!int.TryParse(Console.ReadLine(), out choice))
                {
                    Console.WriteLine("Некорректный ввод. Попробуйте ещё раз.");
                    continue;
                }

                Console.WriteLine();

                switch (choice)
                {
                    case 1:
                        CreateNewMovie(); // Добавление нового фильма
                        break;
                    case 2:
                        DisplayMovieList(); // Вывод списка фильмов
                        break;
                    case 3:
                        SearchMovies(); // Поиск фильмов
                        break;
                    case 4:
                        DeleteMovie(); // Удаление фильма
                        break;
                    case 5:
                        Environment.Exit(0); // Выход из программы
                        break;
                    default:
                        Console.WriteLine("Некорректный номер действия. Попробуйте ещё раз.");
                        break;
                }
            }
        }

        static void CreateNewMovie()
        {
            Movie movie = new Movie();

            Console.WriteLine("Создание новой записи:");
            while (true)
            {
                Console.Write("Введите наименование фильма: ");
                movie.Title = Console.ReadLine();
                if (string.IsNullOrEmpty(movie.Title))
                {
                    Console.WriteLine("Некорректный ввод. Попробуйте ещё раз.");
                    continue;
                }
                break;
            }

            while (true)
            {
                Console.Write("Введите название страны производства фильма: ");
                movie.Country = Console.ReadLine();
                if (string.IsNullOrEmpty(movie.Country) || !movie.Country.All(char.IsLetter)) //Проверка ввода (строка не пустая, в строке только буквы)
                {
                    Console.WriteLine("Некорректный ввод. Попробуйте ещё раз.");
                    continue;
                }
                break;
            }

            while (true)
            {
                Console.Write("Введите дату и время сеанса (в формате ГГГГ-ММ-ДД ЧЧ:ММ): ");
                string input = Console.ReadLine();
                if (!DateTime.TryParseExact(input, "yyyy-MM-dd HH:mm", CultureInfo.InvariantCulture, DateTimeStyles.None, out movie.ShowTime))
                {
                    Console.WriteLine("Некорректный формат даты и времени. Попробуйте еще раз.");
                    continue;
                }
                break;
            }

            while (true)
            {
                Console.Write("Введите общее количество мест: ");
                if (!int.TryParse(Console.ReadLine(), out movie.TotalSeats) || movie.TotalSeats <= 0)
                {
                    Console.WriteLine("Некорректный ввод. Попробуйте еще раз.");
                    continue;
                }
                break;
            }

            while (true)
            {
                Console.Write("Введите количество свободных мест: ");
                if (!int.TryParse(Console.ReadLine(), out movie.AvailableSeats) || !(movie.AvailableSeats <= movie.TotalSeats))
                {
                    Console.WriteLine("Некорректный ввод. Попробуйте еще раз.");
                    continue;
                }
                break;
            }

            while (true)
            {
                Console.Write("Введите стоимость билета: ");
                if (!decimal.TryParse(Console.ReadLine(), out movie.TicketPrice) || movie.TicketPrice <= 0)
                {
                    Console.WriteLine("Некорректная стоимость билета. Попробуйте еще раз.");
                    continue;
                }
                break;
            }

            List<int> validAgeRestrictions = new List<int> { 0, 6, 12, 16, 18 }; // возрастные ограничения в РФ

            while (true)
            {
                Console.Write("Введите возрастные ограничения: ");
                if (!int.TryParse(Console.ReadLine(), out movie.AgeRestrictions) || !validAgeRestrictions.Contains(movie.AgeRestrictions))
                {
                    Console.WriteLine("Некорректные возрастные ограничения. Ввод отменен.");
                    continue;
                }
                break;
            }

            movies.Add(movie);
            Console.WriteLine("Запись успешно добавлена.");
            Console.WriteLine();
        }

        static void DisplayMovieList()
        {
            Console.WriteLine("Список фильмов:");
            Console.WriteLine(" --------------------------------------------------------------------------------------------------------------------------------------");
            Console.WriteLine("| ID | Наименование  кинофильма       | Страна производства | Дата и время сеанса | Всего мест     | Осталось мест  | Стоимость билета |");
            Console.WriteLine("|--------------------------------------------------------------------------------------------------------------------------------------|");
            int iter = 0;
            foreach (var movie in movies)
            {
                iter += 1;
                Console.WriteLine("| {0, -2} | {1,-30} | {2,-19} | {3,-19} | {4,-14} | {5, -14} | {6, -16} |",
                iter, ((movie.Title.Length > 19) ? (movie.Title.Substring(0, 19) + '.' + '.' + '.') : movie.Title) + ' ' + '(' + movie.AgeRestrictions + '+' + ')', movie.Country, movie.ShowTime.ToString("yyyy-MM-dd HH:mm"), movie.AvailableSeats, movie.TotalSeats, movie.TicketPrice);

            }

            Console.WriteLine(" --------------------------------------------------------------------------------------------------------------------------------------");
            Console.WriteLine();
        }

        static void SearchMovies()
        {
            Console.WriteLine("Поиск записей:");
            Console.WriteLine("Выберите тип поиска:");
            Console.WriteLine("1. Топ 3 сеансов с наибольшим количеством проданных билетов после заданного времени и возрастными ограничениями");
            Console.WriteLine("2. Суммарная стоимость билетов для фильма с заданным названием");
            Console.Write("Введите номер типа поиска: ");

            int searchType;
            if (!int.TryParse(Console.ReadLine(), out searchType))
            {
                Console.WriteLine("Некорректный ввод. Поиск отменен.");
                return;
            }

            Console.WriteLine();

            switch (searchType)
            {
                case 1:
                    SearchTopMovies();
                    break;
                case 2:
                    SearchTotalTicketPrice();
                    break;
                default:
                    Console.WriteLine("Некорректный номер типа поиска. Поиск отменен.");
                    break;
            }
        }

        static void SearchTopMovies()
        {
            Console.Write("Введите время дня для поиска сеансов (в формате ЧЧ:ММ): ");
            string searchTimeStr = Console.ReadLine();
            if (!DateTime.TryParse(searchTimeStr, out DateTime searchTime))
            {
                Console.WriteLine("Некорректный формат времени. Поиск отменен.");
                return;
            }

            Console.Write("Введите минимальные возрастные ограничения: ");
            if (!int.TryParse(Console.ReadLine(), out int minAgeRestrictions))
            {
                Console.WriteLine("Некорректные возрастные ограничения. Поиск отменен.");
                return;
            }

            Console.WriteLine("Список фильмов с наибольшим количеством проданных билетов:");
            Console.WriteLine(" --------------------------------------------------------------------------------------------------------------------------------------");
            Console.WriteLine("| ID | Наименование  кинофильма       | Страна производства | Дата и время сеанса | Всего мест     | Осталось мест  | Стоимость билета |");
            Console.WriteLine("|--------------------------------------------------------------------------------------------------------------------------------------|");
            //Выполняем поиск с помощью функции трех сеансов с наибольшим проданных билетов
            List<Movie> topSessions = FindTopSessions(movies, searchTimeStr, minAgeRestrictions, 3);
            //Перебираем эти фильмы, выводим
            int iter = 0;
            foreach (var mov in topSessions)
            {
                iter += 1;
                Console.WriteLine("| {0, -2} | {1,-30} | {2,-19} | {3,-19} | {4,-14} | {5, -14} | {6, -16} |",
                iter, ((mov.Title.Length > 19) ? (mov.Title.Substring(0, 19) + '.' + '.' + '.') : mov.Title) + ' ' + '(' + mov.AgeRestrictions + '+' + ')', mov.Country, mov.ShowTime.ToString("yyyy-MM-dd HH:mm"), mov.AvailableSeats, mov.TotalSeats, mov.TicketPrice);

            }
            Console.WriteLine(" --------------------------------------------------------------------------------------------------------------------------------------");
            Console.WriteLine();
        }
        static List<Movie> FindTopSessions(List<Movie> movies, string desiredTime, int desiredAge, int count)
        {
            DateTime time = DateTime.Parse(desiredTime); // Преобразуем заданное время в формат DateTime

            List<Movie> filteredMovies = movies
                .Where(movie => movie.ShowTime >= time) // Фильтруем фильмы, проходящие после или в то же время, что и заданное время
                .Where(movie => movie.AgeRestrictions >= desiredAge) // Фильтруем фильмы по возрастным ограничениям
                .OrderByDescending(movie => GetSoldTicketsCount(movie)) // Сортируем фильмы по количеству проданных билетов
                .Take(count) // Получаем указанное количество фильмов
                .ToList();

            return filteredMovies;
        }
        static int GetSoldTicketsCount(Movie movie)
        {
            return movie.TotalSeats - movie.AvailableSeats; // Получаем количество проданных билетов
        }

        //Поиск суммы
        static void SearchTotalTicketPrice()
        {
            Console.Write("Введите подстроку названия фильма или нажмите ''Ввод'' для отображения суммы всех проданных билетов за последние 3 дня: ");
            string searchSubstring = Console.ReadLine();
            //получение сегодняшнего дня
            DateTime today = DateTime.Today;
            //три дня назад
            DateTime threeDaysAgo = today.AddDays(-3);
            //сумма
            decimal totalSales = 0;
            //перебор всех фильмов
            foreach (Movie movie in movies)
            {
                //проверка на соответствие подстроке
                if (movie.Title.Contains(searchSubstring))
                {
                    //Проверка на заданную дату и ее соответствие 
                    if (movie.ShowTime >= threeDaysAgo && movie.ShowTime <= today.AddDays(1))
                    {
                        //today.AddDays(1) позволяет включить в подсчет фильмы, показ которых был до полуночи сегодняшнего дня.
                        int soldTickets = movie.TotalSeats - movie.AvailableSeats;
                        decimal salesAmount = soldTickets * movie.TicketPrice;
                        totalSales += salesAmount;
                    }
                }

            }
            Console.WriteLine("Суммарная стоимость билетов для фильма с заданным названием: {0:N}", totalSales + " Рублей");

            Console.WriteLine();
        }

        static void DeleteMovie()
        {
            DisplayMovieList();
            Console.Write("Введите номер записи, которую необходимо удалить: ");
            if (!int.TryParse(Console.ReadLine(), out int index))
            {
                Console.WriteLine("Некорректный номер записи. Удаление отменено.");
                return;
            }

            if (index < 1 || index > movies.Count)
            {
                Console.WriteLine("Некорректный номер записи. Удаление отменено.");
                return;
            }

            movies.RemoveAt(index - 1);
            Console.WriteLine("Запись успешно удалена.");
            Console.WriteLine();
        }
    }
}
