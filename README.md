//Notebook
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

class Note {
    private Date date;
    private String text;

    public Note(Date date, String text) {
        this.date = date;
        this.text = text;
    }

    public Date getDate() {
        return date;
    }

    public String getText() {
        return text;
    }
}
interface View {
    void showMenu();
    void showNotes(List<Note> notes);
    String getInput();
    void showMessage(String message);
}
class ConsoleView implements View {
    private Scanner scanner = new Scanner(System.in);

    @Override
    public void showMenu() {
        System.out.println("1. Добавить запись");
        System.out.println("2. Показать записи");
        System.out.println("3. Сохранить записи в файл");
        System.out.println("4. Загрузить записи из файла");
        System.out.println("5. Выход");
    }

    @Override
    public void showNotes(List<Note> notes) {
        SimpleDateFormat dateFormat = new SimpleDateFormat("dd.MM.yyyy HH:mm");
        for (Note note : notes) {
            System.out.println(dateFormat.format(note.getDate()) + ": " + note.getText());
        }
    }

    @Override
    public String getInput() {
        return scanner.nextLine();
    }

    @Override
    public void showMessage(String message) {
        System.out.println(message);
    }
}
class Presenter {
    private View view;
    private List<Note> notes = new ArrayList<>();

    public Presenter(View view) {
        this.view = view;
    }

    public void start() {
        while (true) {
            view.showMenu();
            String choice = view.getInput();

            switch (choice) {
                case "1":
                    addNote();
                    break;
                case "2":
                    view.showNotes(notes);
                    break;
                case "3":
                    saveNotesToFile();
                    break;
                case "4":
                    loadNotesFromFile();
                    break;
                case "5":
                    view.showMessage("Выход из приложения.");
                    return;
                default:
                    view.showMessage("Неверный выбор. Попробуйте еще раз.");
            }
        }
    }

    private void addNote() {
        SimpleDateFormat dateFormat = new SimpleDateFormat("dd.MM.yyyy HH:mm");
        view.showMessage("Введите дату и время (dd.MM.yyyy HH:mm):");
        String dateString = view.getInput();

        try {
            Date date = dateFormat.parse(dateString);
            view.showMessage("Введите текст записи:");
            String text = view.getInput();

            notes.add(new Note(date, text));
            view.showMessage("Запись добавлена.");
        } catch (Exception e) {
            view.showMessage("Ошибка при добавлении записи.");
        }
    }

    private void saveNotesToFile() {
        try (ObjectOutputStream outputStream = new ObjectOutputStream(new FileOutputStream("notes.dat"))) {
            outputStream.writeObject(notes);
            view.showMessage("Записи сохранены в файл.");
        } catch (IOException e) {
            view.showMessage("Ошибка при сохранении записей в файл.");
        }
    }

    private void loadNotesFromFile() {
        try (ObjectInputStream inputStream = new ObjectInputStream(new FileInputStream("notes.dat"))) {
            notes = (List<Note>) inputStream.readObject();
            view.showMessage("Записи загружены из файла.");
        } catch (IOException | ClassNotFoundException e) {
            view.showMessage("Ошибка при загрузке записей из файла.");
        }
    }
}

public class main {
    public static void main(String[] args) {
        View view = new ConsoleView();
        Presenter presenter = new Presenter(view);
        presenter.start();
    }
}
