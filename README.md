## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER
## NAME: SANTHOSH S
## REG.NO: 212224100052
## AIM:

To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
<img width="558" height="380" alt="example_img" src="https://github.com/user-attachments/assets/0e90c4a2-9fdc-445c-b360-728c47af2cad" />

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:
```c
#include <stdio.h>
#include <string.h>
#include <ctype.h>

char matrix[5][5];

// Generate Playfair matrix
void generate_matrix(char key[]) {
    int used[26] = {0};
    int i, j, k = 0;

    // Treat J as I
    for (i = 0; key[i]; i++) {
        char ch = toupper(key[i]);
        if (ch == 'J') ch = 'I';

        if (isalpha(ch) && used[ch - 'A'] == 0) {
            matrix[k / 5][k % 5] = ch;
            used[ch - 'A'] = 1;
            k++;
        }
    }

    // Fill remaining letters
    for (i = 0; i < 26; i++) {
        if (i + 'A' == 'J') continue;

        if (used[i] == 0) {
            matrix[k / 5][k % 5] = i + 'A';
            used[i] = 1;
            k++;
        }
    }
}

// Find position in matrix
void find_position(char ch, int *row, int *col) {
    int i, j;
    for (i = 0; i < 5; i++) {
        for (j = 0; j < 5; j++) {
            if (matrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

// Preprocess text
void preprocess_text(char text[], char prepared[]) {
    int i = 0, k = 0;

    while (text[i]) {
        if (!isalpha(text[i])) {
            i++;
            continue;
        }

        char ch1 = toupper(text[i]);
        if (ch1 == 'J') ch1 = 'I';

        char ch2;
        if (text[i + 1]) {
            ch2 = toupper(text[i + 1]);
            if (ch2 == 'J') ch2 = 'I';
        } else {
            ch2 = '\0';
        }

        if (!isalpha(ch2)) {
            prepared[k++] = ch1;
            i++;
        }
        else if (ch1 == ch2) {
            prepared[k++] = ch1;
            prepared[k++] = 'X';
            i++;
        }
        else {
            prepared[k++] = ch1;
            prepared[k++] = ch2;
            i += 2;
        }
    }

    if (k % 2 != 0) {
        prepared[k++] = 'X';
    }

    prepared[k] = '\0';
}

// Encrypt
void encrypt(char text[], char cipher[]) {
    int i, k = 0;

    for (i = 0; text[i]; i += 2) {
        int r1, c1, r2, c2;
        find_position(text[i], &r1, &c1);
        find_position(text[i + 1], &r2, &c2);

        if (r1 == r2) {
            cipher[k++] = matrix[r1][(c1 + 1) % 5];
            cipher[k++] = matrix[r2][(c2 + 1) % 5];
        }
        else if (c1 == c2) {
            cipher[k++] = matrix[(r1 + 1) % 5][c1];
            cipher[k++] = matrix[(r2 + 1) % 5][c2];
        }
        else {
            cipher[k++] = matrix[r1][c2];
            cipher[k++] = matrix[r2][c1];
        }
    }

    cipher[k] = '\0';
}

// Decrypt
void decrypt(char cipher[], char plain[]) {
    int i, k = 0;

    for (i = 0; cipher[i]; i += 2) {
        int r1, c1, r2, c2;
        find_position(cipher[i], &r1, &c1);
        find_position(cipher[i + 1], &r2, &c2);

        if (r1 == r2) {
            plain[k++] = matrix[r1][(c1 - 1 + 5) % 5];
            plain[k++] = matrix[r2][(c2 - 1 + 5) % 5];
        }
        else if (c1 == c2) {
            plain[k++] = matrix[(r1 - 1 + 5) % 5][c1];
            plain[k++] = matrix[(r2 - 1 + 5) % 5][c2];
        }
        else {
            plain[k++] = matrix[r1][c2];
            plain[k++] = matrix[r2][c1];
        }
    }

    plain[k] = '\0';
}

// MAIN
int main() {
    char key[100], plain[100], prepared[200], cipher[200], decrypted[200];
    int i, j;

    printf("Enter the key: ");
    fgets(key, sizeof(key), stdin);

    generate_matrix(key);

    printf("\nPlayfair Matrix:\n");
    for (i = 0; i < 5; i++) {
        for (j = 0; j < 5; j++) {
            printf("%c ", matrix[i][j]);
        }
        printf("\n");
    }

    printf("\nEnter the plain text: ");
    fgets(plain, sizeof(plain), stdin);

    preprocess_text(plain, prepared);
    encrypt(prepared, cipher);
    decrypt(cipher, decrypted);

    printf("\nPrepared Text : %s\n", prepared);
    printf("Encrypted Text: %s\n", cipher);
    printf("Decrypted Text: %s\n", decrypted);

    return 0;
}
```


## Output:
<img width="381" height="367" alt="Screenshot 2026-04-27 091241" src="https://github.com/user-attachments/assets/74cc9286-9ec9-4906-ae7f-6e3bc2b9b688" />

## Result:
Thus the implementation of the Playfair Substitution technique is executed successfully.
