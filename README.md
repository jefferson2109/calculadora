import java.util.*;

public class CalculadoraPolonesa {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("Escolha a notação da expressão (infixa, posfixa, prefixa):");
        String tipoNotacao = scanner.nextLine().toLowerCase();

        System.out.println("Digite a expressão:");
        String expressao = scanner.nextLine();

        String infixa = "", posfixa = "", prefixa = "";
        double resultado = 0;

        switch (tipoNotacao) {
            case "infixa":
                infixa = expressao;
                posfixa = infixaParaPosfixa(expressao);
                prefixa = infixaParaPrefixa(expressao);
                resultado = avaliarPosfixa(posfixa);
                break;
            case "posfixa":
                posfixa = expressao;
                infixa = posfixaParaInfixa(expressao);
                prefixa = infixaParaPrefixa(infixa);
                resultado = avaliarPosfixa(posfixa);
                break;
            case "prefixa":
                prefixa = expressao;
                infixa = prefixaParaInfixa(expressao);
                posfixa = infixaParaPosfixa(infixa);
                resultado = avaliarPosfixa(posfixa);
                break;
            default:
                System.out.println("Notação inválida.");
                return;
        }

        System.out.println("Expressão Infixa: " + infixa);
        System.out.println("Expressão Pós-fixa: " + posfixa);
        System.out.println("Expressão Pré-fixa: " + prefixa);
        System.out.println("Resultado: " + resultado);
    }

    static int precedencia(char operador) {
        if (operador == '+' || operador == '-') return 1;
        if (operador == '*' || operador == '/') return 2;
        return -1;
    }

    static String infixaParaPosfixa(String expressao) {
        StringBuilder resultado = new StringBuilder();
        Stack<Character> pilha = new Stack<>();

        for (char c : expressao.replaceAll(" ", "").toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                resultado.append(c);
            } else if (c == '(') {
                pilha.push(c);
            } else if (c == ')') {
                while (!pilha.isEmpty() && pilha.peek() != '(') {
                    resultado.append(pilha.pop());
                }
                pilha.pop();
            } else {
                while (!pilha.isEmpty() && precedencia(c) <= precedencia(pilha.peek())) {
                    resultado.append(pilha.pop());
                }
                pilha.push(c);
            }
        }

        while (!pilha.isEmpty()) {
            resultado.append(pilha.pop());
        }
        return resultado.toString();
    }

    static String infixaParaPrefixa(String expressao) {
        String invertida = new StringBuilder(expressao).reverse().toString();
        invertida = invertida.replace('(', 'X').replace(')', '(').replace('X', ')');
        String posfixa = infixaParaPosfixa(invertida);
        return new StringBuilder(posfixa).reverse().toString();
    }

    static String posfixaParaInfixa(String expressao) {
        Stack<String> pilha = new Stack<>();
        for (char c : expressao.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                pilha.push(String.valueOf(c));
            } else {
                String op2 = pilha.pop();
                String op1 = pilha.pop();
                pilha.push("(" + op1 + c + op2 + ")");
            }
        }
        return pilha.peek();
    }

    static String prefixaParaInfixa(String expressao) {
        Stack<String> pilha = new Stack<>();
        for (int i = expressao.length() - 1; i >= 0; i--) {
            char c = expressao.charAt(i);
            if (Character.isLetterOrDigit(c)) {
                pilha.push(String.valueOf(c));
            } else {
                String op1 = pilha.pop();
                String op2 = pilha.pop();
                pilha.push("(" + op1 + c + op2 + ")");
            }
        }
        return pilha.peek();
    }

    static double avaliarPosfixa(String expressao) {
        Stack<Double> pilha = new Stack<>();
        for (char c : expressao.toCharArray()) {
            if (Character.isDigit(c)) {
                pilha.push((double) (c - '0'));
            } else {
                double b = pilha.pop();
                double a = pilha.pop();
                switch (c) {
                    case '+': pilha.push(a + b); break;
                    case '-': pilha.push(a - b); break;
                    case '*': pilha.push(a * b); break;
                    case '/': pilha.push(a / b); break;
                }
            }
        }
        return pilha.pop();
    }
}
