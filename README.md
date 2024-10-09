#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct book {
    char name[30];
    char author[30];
    int id;
    struct book *next;
};

struct student {
    char name[30];
    char email[30];
    char book[30];
    char author[30];
    int id;
    struct student *next;
};

struct book *start_lib = NULL;
struct student *start = NULL;

struct book *initialize_lib(struct book *);
struct student *book_issue(struct student *);
struct student *book_return(struct student *);
void display_students(struct student *);
void greetings();
void main_menu();
struct book *delete_book(int);
struct book *add_book(char [], char [], int);

int main() {
    start_lib = initialize_lib(start_lib);
    greetings();
    main_menu();
    return 0;
}

void greetings() {
    printf("\n\n");
    printf("\t\t\t     **************\n");
    printf("\t\t\t     *                                      *\n");
    printf("\t\t\t     *     ----------------------------     *\n");
    printf("\t\t\t     *      WELCOME TO STUDENT LIBRARY      *\n");
    printf("\t\t\t     *     ----------------------------     *\n");
    printf("\t\t\t     **************\n");
    printf("\n\n");
    printf("\t\t\t             Press any key to continue: ");
    getchar();  // Changed to getchar() for portability
}

void main_menu() {
    int choice;
    do {
        printf("\n\n");
        printf("\n\t\t\t*****************\n");
        printf("\n\t\t\t\t      MAIN MENU: ");
        printf("\n\t\t\t\t     1. ISSUE OF BOOKS ");
        printf("\n\t\t\t\t     2. RETURN OF BOOKS ");
        printf("\n\t\t\t\t     3. DISPLAY STUDENT DETAILS ");
        printf("\n\t\t\t\t     4. EXIT\n ");
        printf("\n\t\t\t*****************\n");
        printf("\n\t\t\t\t      Enter your choice: ");
        
        if (scanf("%d", &choice) != 1) {
            printf("\nInvalid input! Please enter a number.\n");
            while (getchar() != '\n');  // Clear invalid input
            continue;
        }

        switch (choice) {
            case 1:
                start = book_issue(start);
                break;
            case 2:
                start = book_return(start);
                break;
            case 3:
                display_students(start);
                break;
            case 4:
                exit(0);
            default:
                printf("\n\t\t\t\t      ...Invalid Option!...\n");
        }
    } while (choice != 4);
}

struct book *initialize_lib(struct book *start) {
    struct book *new_book;
    char *book_names[] = {"The Kite Runner", "To Kill A Mockingbird", "The Alchemist", "Pride And Prejudice", "A Tale Of Two Cities"};
    char *authors[] = {"Khaled Hosseini", "Harper Lee", "Paulo Coelho", "Jane Austen", "Charles Dickens"};
    int ids[] = {101, 102, 103, 104, 105};

    for (int i = 0; i < 5; i++) {
        new_book = (struct book *)malloc(sizeof(struct book));
        strcpy(new_book->name, book_names[i]);
        strcpy(new_book->author, authors[i]);
        new_book->id = ids[i];
        new_book->next = start;
        start = new_book;
    }
    return start;
}

struct student *book_issue(struct student *start) {
    struct book *ptr = start_lib;
    struct student *new_student;
    int id, flag = 0;

    if (!start_lib) {
        printf("\n\t\t\t\t No books left in the library to issue!\n");
        return start;
    }

    printf("\n\t****** Books Available: *******\n");
    while (ptr) {
        printf("\n\t Book Title: %s\n\t Author: %s\n\t Book ID: %d\n", ptr->name, ptr->author, ptr->id);
        ptr = ptr->next;
    }
    
    printf("\n\t Enter the Book ID: ");
    scanf("%d", &id);

    ptr = start_lib;
    while (ptr) {
        if (ptr->id == id) {
            flag = 1;
            break;
        }
        ptr = ptr->next;
    }

    if (flag) {
        new_student = (struct student *)malloc(sizeof(struct student));
        printf("\n\t Enter Student Details:\n ");
        printf("\n\t Enter your Name: ");
        scanf("%s", new_student->name);
        printf("\n\t Enter your Email: ");
        scanf("%s", new_student->email);
        strcpy(new_student->book, ptr->name);
        strcpy(new_student->author, ptr->author);
        new_student->id = ptr->id;
        new_student->next = start;
        start = new_student;

        start_lib = delete_book(id);
        printf("\n\t Issue of Book ID %d done successfully!\n", new_student->id);
    } else {
        printf("\n\t\t      ...Invalid Book ID!...\n");
    }
    
    printf("\n\n\t Press any key to go to the main menu: ");
    getchar();  // To consume the newline left by previous scanf
    getchar();  // Wait for user input
    return start;
}

struct student *book_return(struct student *start) {
    struct student *ptr = start;
    struct student *preptr = NULL;
    int identity, flag = 0;

    printf("\n\n\t****** Books Submission: *******\n");
    printf("\n\n\t Enter your Book ID: ");
    scanf("%d", &identity);

    while (ptr) {
        if (ptr->id == identity) {
            flag = 1;
            break;
        }
        preptr = ptr;
        ptr = ptr->next;
    }

    if (flag) {
        printf("\n\t_________________\n");
        printf("\n\t Student Name: %s\n\t Student Email: %s\n\t Name of Book Issued: %s\n\t Book ID: %d\n",
               ptr->name, ptr->email, ptr->book, ptr->id);
        printf("\n\t_________________\n");

        if (preptr) {
            preptr->next = ptr->next;
        } else {
            start = ptr->next;  // Remove the first student if needed
        }
        
        char bookname[30], authorname[30];
        strcpy(bookname, ptr->book);
        strcpy(authorname, ptr->author);
        free(ptr);
        add_book(bookname, authorname, identity);
        
        printf("\n\t Return of Book ID %d done successfully!\n", identity);
    } else {
        printf("\n\tSorry, the book doesn't exist! Please recheck the entered ID.");
    }
    
    printf("\n\n\t Press any key to go to the main menu: ");
    getchar();  // To consume the newline left by previous scanf
    getchar();  // Wait for user input
    return start;
}

void display_students(struct student *start) {
    struct student *ptr = start;
    if (!ptr) {
        printf("\n\tNo students have issued any books.\n");
        return;
    }

    printf("\n\t***** Details of Students: ******\n");
    while (ptr) {
        printf("\n\t_________________\n");
        printf("\n\t\t Student Name: %s\n\t\t Student Email: %s\n\t\t Name of Book Issued: %s\n\t\t Book ID: %d\n",
               ptr->name, ptr->email, ptr->book, ptr->id);
        printf("\n\t_________________\n");
        ptr = ptr->next;
    }
    printf("\n\n\t Press any key to go to the main menu: ");
    getchar();  // To consume the newline left by previous scanf
    getchar();  // Wait for user input
}

struct book *delete_book(int id) {
    struct book *ptr = start_lib, *preptr = NULL;

    while (ptr) {
        if (ptr->id == id) {
            if (preptr) {
                preptr->next = ptr->next;
            } else {
                start_lib = ptr->next;  // Handle deleting the first book
            }
            free(ptr);
            return start_lib;
        }
        preptr = ptr;
        ptr = ptr->next;
    }
    return start_lib;  // Return unchanged if the book was not found
}

struct book *add_book(char bookname[], char authorname[], int id) {
    struct book *new_book = (struct book *)malloc(sizeof(struct book));
    strcpy(new_book->name, bookname);
    strcpy(new_book->author, authorname);
    new_book->id = id;
    new_book->next = start_lib;
    start_lib = new_book;
    return start_lib;
}
