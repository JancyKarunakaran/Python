package session1.userinput;
import java.io.*;
import java.util.*;
import javax.swing.*;

public class MultipleChoiceQuiz {

    JFrame f;
    MultipleChoiceQuiz(Question question){
        f = question.frame;
        f.setSize(800,500);
        f.setLayout(null);
        f.setVisible(true);
    }

    public static void main(String[] args) throws IOException {
        // Read the questions from the CSV file
        //new RadioButtonExample();
        List<Question> questions = readQuestionsFromCSV("/Users/janet/IdeaProjects/Hello world/src/session1/userinput/trivia.csv");
        if (questions.isEmpty()) {
            System.out.println("No questions found in file.");
            return;
        }

        // Run the quiz loop
        Scanner scanner = new Scanner(System.in);
        int score = 0;
        do {
            // Choose a random question
            Question question = questions.get((int)(Math.random() * questions.size()));
            System.out.println(question);
            new MultipleChoiceQuiz(question);

            // Get the user's answer
            System.out.print("Your answer: ");
            String answer = scanner.nextLine();

            // Check the answer and update the score
            if (question.isCorrectAnswer(answer)) {
                System.out.println("Correct!");
                score++;
            } else {
                System.out.println("Incorrect. The correct answer was: " + question.getCorrectAnswer());
            }

            // Ask the user if they want to continue
            System.out.print("Continue? (y/n) ");
        } while (scanner.nextLine().toLowerCase().startsWith("y"));

        // Display the final score
        System.out.println("Your score is " + score + " out of " + questions.size());
    }

    private static List<Question> readQuestionsFromCSV(String fileName) throws IOException {
        List<Question> questions = new ArrayList<>();
        BufferedReader reader = new BufferedReader(new FileReader(fileName));
        String line;
        while ((line = reader.readLine()) != null) {
            String[] fields = line.split(",");
            if (fields.length < 3) {
                System.err.println("Invalid line: " + line);
                continue;
            }
            String question = fields[0];
            String[] options = Arrays.copyOfRange(fields, 1, fields.length - 1);
            String correctAnswer = fields[fields.length - 1];
            questions.add(new Question(question, options, correctAnswer));
        }
        reader.close();
        return questions;
    }

    private static class Question {
        private final JLabel label;

        private final String[] options;
        private final String question;
        private final JFrame frame;
        private final String correctAnswer;

        private final ButtonGroup group;

        public Question(String question, String[] options, String correctAnswer) {
            this.label = new JLabel(question);
            this.question = question;
            this.options = options;
            this.frame = new JFrame();
            this.group = new ButtonGroup();
            label.setBounds(75,10,700,30);
            frame.add(label);
            for(int i = 0; i < options.length; i++){
                JRadioButton j = new JRadioButton(options[i]);
                j.setBounds(75,(i+1)*50,700,30);
                group.add(j);
                frame.add(j);
            }
            this.correctAnswer = correctAnswer;
        }

        public boolean isCorrectAnswer(String answer) {
            return answer.equals(correctAnswer);
        }

        public String getCorrectAnswer() {
            return correctAnswer;
        }

        public String toString() {
            StringBuilder sb = new StringBuilder(question).append("\n");
            for (int i = 0; i < options.length; i++) {
                sb.append((char)('a' + i)).append(") ").append(options[i]).append("\n");
            }
            return sb.toString();
        }
    }
}
