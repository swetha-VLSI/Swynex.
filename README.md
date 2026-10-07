#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>

#define MAX_LINE 256

/* Check whether a string contains only digits */
int isValidAge(const char *age) {
    if (*age == '\0')
        return 0;

    for (int i = 0; age[i] != '\0'; i++) {
        if (!isdigit((unsigned char)age[i]))
            return 0;
    }

    int value = atoi(age);

    return value >= 1 && value <= 100;
}

/* Validate one CSV line */
int validateLine(char *line, char *name, char *age, char *department) {
    char *token;

    token = strtok(line, ",");
    if (token == NULL)
        return 0;
    strcpy(name, token);

    token = strtok(NULL, ",");
    if (token == NULL)
        return 0;
    strcpy(age, token);

    token = strtok(NULL, ",");
    if (token == NULL)
        return 0;
    strcpy(department, token);

    /* Remove newline from department */
    department[strcspn(department, "\r\n")] = '\0';

    /* Validate fields */
    if (strlen(name) == 0 || strlen(department) == 0)
        return 0;

    if (!isValidAge(age))
        return 0;

    return 1;
}

int main(void) {
    FILE *inputFile;
    FILE *outputFile;

    char line[MAX_LINE];
    char name[100], age[20], department[100];

    int lineNumber = 0;
    int validCount = 0;
    int invalidCount = 0;

    inputFile = fopen("input.csv", "r");

    /* Handle missing input file */
    if (inputFile == NULL) {
        printf("Error: input.csv file not found.\n");
        return 1;
    }

    outputFile = fopen("output.csv", "w");

    if (outputFile == NULL) {
        printf("Error: Unable to create output.csv.\n");
        fclose(inputFile);
        return 1;
    }

    /* Write header */
    fprintf(outputFile, "Name,Age,Department\n");

    /* Process each line */
    while (fgets(line, sizeof(line), inputFile) != NULL) {
        lineNumber++;

        /* Skip header */
        if (lineNumber == 1)
            continue;

        if (validateLine(line, name, age, department)) {

            /* Write processed valid record */
            fprintf(outputFile, "%s,%s,%s\n",
                    name, age, department);

            validCount++;
        } else {
            printf("Invalid line %d skipped.\n", lineNumber);
            invalidCount++;
        }
    }

    fclose(inputFile);
    fclose(outputFile);

    printf("\nProcessing completed successfully.\n");
    printf("Valid records   : %d\n", validCount);
    printf("Invalid records : %d\n", invalidCount);
    printf("Output file     : output.csv\n");

    return 0;
}
