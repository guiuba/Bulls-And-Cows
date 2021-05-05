This is my implementation for the classic code breaking game "Bulls and Cows", a Jet Brains Academy project (stage 7/7)

How program works:

![image](https://user-images.githubusercontent.com/69851038/117127841-01ebb180-ad73-11eb-82bd-db045b01e929.png)
---------------------
My code:

import java.util.Arrays;

import java.util.Random;

import java.util.Scanner;

public class Main {
    static int turn = 1;
    static int cows = 0;
    static int bulls = 0;
    static Scanner scan = new Scanner(System.in);
    static String secretCode;
    static int digits;
    static int symbols;
    static Random random = new Random();
    static int charRange = 0;
    static int maxNum = 9;
    static boolean isValidInput = true;

    public static void main(String[] args) {
        System.out.println("Input the length of the secret code:");
        digits = checkInput(scan.nextLine());
        if (isValidInput) {
            System.out.println("Input the number of possible symbols in the code:");
            symbols = checkInput(scan.nextLine());
        }
        if (isValidInput) {
            if (parseValues()) {
                secretCode = generateCode(digits, symbols);
                playGame();
            }
        }
    }

    public static int checkInput(String scanned) {
        boolean numeric;
        numeric = scanned.matches("\\d+");
        if (!numeric) {
            System.out.printf("Error: \"%s\" isn't a valid number.", scanned);
            isValidInput = false;
            return 0;
        } else {
            int aux = Integer.parseInt(scanned);
            if(aux > 36) {
                System.out.println("Error: maximum number of possible symbols" +
                        " in the code is 36 (0-9, a-z).");
                isValidInput = false;
                return 0;
            } else if(aux == 0) {
                System.out.println("Error: minimum number of possible symbols" +
                        " in the code is 1 (0-9, a-z).");
                isValidInput = false;
                return 0;
            }
        }
        return Integer.parseInt(scanned);
    }

        public static boolean parseValues() {
            if (digits > symbols) {
                System.out.printf("Error: it's not possible to generate a code with " +
                        "a length of %d with %d unique symbols.", digits, symbols);
                return false;
            }
                return true;
        }

        public static String processGuess () {
            StringBuilder sb = new StringBuilder("Grade: ");
            System.out.printf("Turn %d:\n", turn);
            String guess = scan.next();
            System.out.println("guess: " + guess);
            String[] guessArray = guess.split("");
            System.out.println("guessArray: " + Arrays.toString(guessArray));

            String[] secretCodeArray = secretCode.split("");
            for (int i = 0; i < guessArray.length; i++) {
                if (guessArray[i].equals(secretCodeArray[i])) {
                    bulls++;
                }
                if (secretCode.contains(guessArray[i])) {
                    ++cows;
                }
            }
            cows = cows - bulls;
            if (bulls > 0) {
                sb.append(String.format("%d bull(s)", bulls));
            }
            if (bulls > 0 && cows > 0) {
                sb.append(" and ");
            }
            if (cows > 0) {
                sb.append(String.format("%d cow(s)", cows));
            }

            if (bulls == 0 && cows == 0) {
                sb.append("none");
            }
            if (bulls == digits) {

                sb.append("\nCongratulations! You guessed the secret code.");
                scan.close();
                return sb.toString();
            }
            bulls = 0;
            cows = 0;
            return sb.toString();
        }

        public static void playGame () {
            String stars = "*".repeat(digits);
            System.out.printf("The secret is prepared: %s (0-%d, a-%c).\n", stars, maxNum, (charRange + 96));
            System.out.println("Okay, let's start a game!");
            System.out.println("digits: " + digits);
            System.out.println("secretCode: " + secretCode);

            while (bulls != digits) {
                System.out.println(processGuess());
                turn++;
            }
        }

        public static String generateCode ( int digits, int symbols){
            System.out.println("digits: " + digits);
            System.out.println("symbols: " + symbols);
            StringBuilder sb = new StringBuilder();
            charRange = symbols - 10;
            if (symbols < 10) {
                maxNum = symbols - 1;
            }
            System.out.println("charRange: " + charRange);
            int charInputs = 0;
            if (charRange > 0) {
                charInputs = random.nextInt(digits + 1);
            }

            System.out.println("charInputs: " + charInputs);
            int numberInputs = digits - charInputs;
            System.out.println("numberInputs: " + numberInputs);

            if (numberInputs == 1) {
                sb.append(random.nextInt(10));
            }
            if (numberInputs > 1) {
                String aux = pseudoRandomNumber().substring(0, numberInputs);
                int[] sortedNumber = sortNumber(aux, numberInputs);
                boolean hasUniqueDigits = false;
                int count = 0;
                while (!hasUniqueDigits) {
                    for (int i = 1; i < numberInputs; i++) {
                        if (sortedNumber[i - 1] == sortedNumber[i]) {
                            aux = pseudoRandomNumber().substring(0, numberInputs);
                            sortedNumber = sortNumber(aux, numberInputs);
                            i = 0;
                        } else {
                            count++;
                            if (count == numberInputs - 1) {
                                hasUniqueDigits = true;
                            }
                        }
                    }
                }
                sb.append(aux);
            }
            System.out.println("sb.length(): " + sb.length());


            if (charRange > 0) {
                for (int i = 0; i < charInputs; i++) {
                    char charAux2 = (char) (random.nextInt(charRange) + 97);
                    while (sb.toString().contains(String.valueOf(charAux2))) {
                        charAux2 = (char) (random.nextInt(charRange) + 97);
                    }
                    sb.insert(random.nextInt(sb.length() != 0 ? sb.length() : 1), charAux2);
                }
            }

            return sb.toString();
        }
        public static int[] sortNumber (String toSort,int digits){
            int[] sortedNumber = new int[digits];
            for (int i = 0; i < digits; i++) {
                sortedNumber[i] = Integer.parseInt(String.valueOf(toSort.charAt(i)));
            }
            Arrays.sort(sortedNumber);
            return sortedNumber;
        }


        public static String pseudoRandomNumber () {
            return String.valueOf(Math.abs(random.nextLong()));
        }

}
