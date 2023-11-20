import java.util.*;
import java.util.stream.Collectors;

import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;

public class PersonalityTest {

    private static final List<String> QUESTIONS = Arrays.asList(
        "¿Prefieres pasar tiempo con otras personas o solo?",
        "¿Te consideras una persona introvertida o extrovertida?",
        "¿Te consideras una persona racional o emocional?",
        "¿Te consideras una persona organizada o desorganizada?",
        "¿Te consideras una persona práctica o creativa?",
        "¿Cómo te llamas?",
        "¿Qué tan motivado estás para alcanzar tus objetivos?",
        "¿Cuáles son tus valores más importantes?",
        "¿En qué eres bueno?",
        "¿Cuáles son tus fortalezas y debilidades?",
        "¿Cuáles son tus metas personales y profesionales?",
        "¿Cómo te sientes cuando estás estresado?",
        "¿Cómo manejas tus emociones?",
        "¿Cómo tomas decisiones?"
    );

    private static final List<String> PERSONALITY_TYPES = Arrays.asList(
        "ESTJ",
        "ESTP",
        "ISTJ",
        "ISTP",
        "ESFJ",
        "ESFP",
        "ISFJ",
        "ISFP"
    );

    private static final String SECRET_KEY = "Carlossec98";

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Obtenemos el nombre del usuario
        System.out.println("¿Cómo te llamas? ");
        String name = scanner.nextLine();

        // Creamos un modelo de aprendizaje automático
        LogisticRegression model = new LogisticRegression();

        // Entrenamos el modelo
        List<Boolean> answers = QUESTIONS.stream()
            .map(scanner::nextLine)
            .map(answer -> answer.equals("sí"))
            .collect(Collectors.toList());
        model.fit(answers, PERSONALITY_TYPES);

        // Validamos las respuestas del usuario
        for (int i = 0; i < QUESTIONS.size(); i++) {
            if (answers.get(i) != answers.get(QUESTIONS.size() - 1 - i)) {
                System.out.println("Las respuestas a las preguntas " + i + " y " + (QUESTIONS.size() - 1 - i) + " no coinciden. Por favor, responde a estas preguntas de nuevo.");
                return;
            }
        }

        // Obtenemos la respuesta del usuario
        answers = QUESTIONS.stream()
            .map(scanner::nextLine)
            .map(answer -> answer.equals("sí"))
            .collect(Collectors.toList());

        // Predecimos el tipo de personalidad del usuario
        String personalityType = model.predict(answers)[0];

        // Encriptamos las respuestas del usuario
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        SecretKeySpec keySpec = new SecretKeySpec(SECRET_KEY.getBytes(), "AES");
        cipher.init(Cipher.ENCRYPT_MODE, keySpec);
        byte[] encryptedAnswers = cipher.doFinal(answers.stream().mapToInt(Boolean::byteValue).toArray());

        // Almacenamos las respuestas del usuario en una base de datos
        Database database = new Database();
        database.insertAnswers(name, encryptedAnswers);

        // Mostramos los resultados al usuario
        System.out.println("Tu tipo de personalidad es " + personalityType + ".");

        // Inicializamos el chatbot
        Chatbot chatbot = new Chatbot(name, personalityType);

        // Comenzamos la conversación con el chatbot
        chatbot.startConversation();
    }

    public static class Chatbot {

        private static final List<String> EMOCIONES = Arrays.asList(
            "feliz",
            "triste",
            "enojado",
            "asustado",
            "sorprendido"
        );

        private static final List<String> PREGUNTAS_DE_IDENTIFICACION_DE_EMOCIONES = Arrays.asList(
            "¿Cómo te sientes?",
            "¿Qué te hace


