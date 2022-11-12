# Lab4.2
```ruby
#include <stdio.h>
#include <stdlib.h>
#include <locale.h>
#include <windows.h>

void PrintCLR(int clr);
void memAlloc(int*** Arr, int N, int M);
void transpose(int*** matr, int countRow, int countColumn);
int** MatrixMul(int** matr1, int** matr2, int countRow1, int countRow2, int countColumn1, int countColumn2);
void ScanVallOffMatrix(int MbMatrix, int** matr, int countRow, int countColumn);
void Tabulation(int value);
int discharge(int Value, int Discharge);

int main() {

  int Na, Nb, Mb;
  int CNT = 0;

  SetConsoleOutputCP(1251);
  SetConsoleCP(1251);
  //===================================== скануємо розмір матриці ===================================== //
  ScanSizeOffMatrix(&Na, &Nb, &Mb);
  //===================================== виділяєм пам'ять під матриці ===================================== //
  int** A = NULL;
  memAlloc(&A, Na, Na);

  int** B = NULL;
  memAlloc(&B, Nb, Mb);
  //===================================== чим будем заповнювати матриці ===================================== //
  int variant;
  ScanContntMatrix(&variant);
  //===================================== заповнення матриць ===================================== //
  switch (variant) {
    case 1:
      ScanVallOffMatrix(1, A, Na, Na);
      ScanVallOffMatrix(2, B, Nb, Mb);
      break;
    case 2:
      for (int i = 0; i < Na; i++) {
        for (int j = 0; j < Na; j++) {
          A[i][j] = rand();
        }
      }
      for (int i = 0; i < Nb; i++) {
        for (int j = 0; j < Mb; j++) {
          B[i][j] = rand();
        }
      }
      break;
    case 3:
      CNT = 0;
      for (int i = 0; i < Na; i++) {
        for (int j = 0; j < Na; j++) {
          A[i][j] = CNT;
          CNT += 2;
        }
      }
      CNT = 0;
      for (int i = 0; i < Nb; i++) {
        for (int j = 0; j < Mb; j++) {
          B[i][j] = CNT;
          CNT += 2;
        }
      }
      break;
    case 4:
      CNT = 0;
      for (int i = 0; i < Na; i++) {
        for (int j = 0; j < Na; j++) {
          A[i][j] = CNT;
          CNT ++;
        }
      }
      CNT = 0;
      for (int i = 0; i < Nb; i++) {
        for (int j = 0; j < Mb; j++) {
          B[i][j] = CNT;
          CNT ++;
        }
      }
      break;
  }
  //===================================== вивід контенту матриць ===================================== //
  PrintMatrixContent(1, A, Na, Na);

  PrintMatrixContent(2, B, Nb, Mb);
  printf("\n\n");
  //=====================================

  int loop = 1;
  do {
    printf("Choose variant:\n 1. Знайти мінімальне значення нижче головної діагоналі і максимальне значення матриці");
    printf("\n 2. Транспортування матриці B");
    printf("\n 3. Множення матриць A*B");
    printf("\n 4. Розташувати всі  елементи матриці у порядку зростання матриці A");
    printf("\n 5. Виведення на екран суму елементів рядків матриці А та стовпців матриці Б\n");

    printf("\n\n");
    scanf("%d", &variant);


    if (variant == 1 ) {
      int Amax = -2147483648;
      int Amin = 2147483647;
      for (int i = 0; i < Na; i++) {
        for (int j = 0; j < Na; j++) {
          if (Amax < A[i][j]) Amax = A[i][j];
          if (j < i && Amin > A[i][j]) Amin = A[i][j]; //j < i - нижче головної діагоналі

        }
      }
      printf("A MAX: %d\n", Amax); //максимальний елемент матриці А
      printf("A MIN under diagonal: %d\n", Amin); //мінімальний з елементів, що знаходяться нижче головної діагоналі матриці А
    }

    if (variant == 2) {
      transpose(B, Mb, Nb);
      PrintMatrixContent(3, B, Mb, Nb);
      for (int i = 0; i < Mb; i++) {
        free(B[i]);
      }
      free(B);
    }

    if (variant == 3) {
      int** AB = MatrixMul(A, B, Na, Na, Nb, Mb);
      PrintMatrixContent(4, AB, Na, Mb);
      for (int i = 0; i < Na; i++) {
        free(AB[i]);
      }
      free(AB);
    }

    if (variant == 4) {

      int TOP_V[Na * Na + 1];
      int DropList = 0;
      unsigned long DropMark = 0;
      int NB = Na * Na;

      for (int i = 0; i < Na; i++) {
        for (int j = 0; j < Na; j++) {
          for (unsigned long i = 0; i < Na * Na; i++) {
            if (DropList == 0) {
              if (TOP_V[i] < A[i][j]) { // перевірка на менше значення
                DropMark = i; //
                DropList = 1;
              }
            } else { // здвиг всього массиву
              if (TOP_V[NB - i] < A[i][j]) { // без цьої фігні будуть баги
                TOP_V[NB - i] = (double)TOP_V[(NB - 1) - i];
              }
            }
          }
          TOP_V[DropMark] = A[i][j];
        }
      }

      printf("TOP:\n");
      unsigned long StNb = 0;
      for (byte i = 0; i < Na * Na; i++) {
        printf("%d,", TOP_V[StNb]);
      }
    }
    if (variant == 5) {
      printf("A sums:\n");
      for (int i = 0; i < Na; i++) {
        int sum = 0;
        for (int j = 0; j < Na; j++) {
          sum += A[i][j];
        }
        printf("%d sum: %d\n", i, sum);
      }

      printf("\n B sums:\n");
      for (int i = 0; i < Mb; i++) {
        int sum = 0;
        for (int j = 0; j < Nb; j++) {
          sum += B[j][i];
        }
        printf("%d sum: %d\n", i, sum);
      }
    }


    printf("\n \n \n Вибрати іншу дію ?\n 1. Так\n 2.Ні\n");
  } while (scanf("%d", &loop) && loop == 1);


  for (int i = 0; i < Na; i++) {
    free(A[i]);
  }
  free(A);
  for (int i = 0; i < Nb; i++) {
    free(B[i]);
  }
  free(B);

  return 0;

}
void PrintCLR(int clr) {
  if (clr) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | BACKGROUND_INTENSITY);
  } else {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11 | 11);
  }
  /*
    Name         | Value
               |
    Black        |   0
    Blue         |   1
    Green        |   2
    Cyan         |   3
    Red          |   4
    Magenta      |   5
    Brown        |   6
    Light Gray   |   7
    Dark Gray    |   8
    Light Blue   |   9
    Light Green  |   10
    Light Cyan   |   11
    Light Red    |   12
    Light Magenta|   13
    Yellow       |   14
    White        |   15
  */
}
void memAlloc(int*** Arr, int N, int M) {
  *Arr = (int**)calloc(N, sizeof(int*)); 
  if (*Arr == NULL) {
    printf("Error");
    exit(0);
  }
  for (int i = 0; i < N; i++) {
    (*Arr)[i] = (int*)calloc(M, sizeof(int));
    if ((*Arr)[i] == NULL) {
      printf("Error");
      exit(0);
    }
  }
}
void ScanSizeOffMatrix(int *Na, int *Nb, int *Mb) {
  system("cls");

  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14 | 14);
  printf("\nВведіть розмір матриць:   \n"); -

  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 11 | 11);

  printf("\n   Кількість рядків-стовбців матриці А: ");
  scanf("%d", Na);

  printf("\n   Кількість рядків матриці B: ");
  scanf("%d", Nb);

  printf("\n   Кількість стовбців матриці B: ");
  scanf("%d", Mb);

  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | 0);
  system("cls");
}
void ScanContntMatrix(int *variant) {
  int X_cursor;
  int Loop = 1;
  while (Loop) {
    PrintCLR(0);
    printf("\n ");
    system("cls");

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | 0);
    printf("\n Використовуйте стрілки /\ і \/ на клавіатурі для переміщення по пунктам і Enter для вибиру варіанта \n\n");

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14 | 14);
    printf("\n   Оберіть варіант наповнення матриць інформацією:  ");

    X_cursor == 0 ? PrintCLR(1) : PrintCLR(0);
    printf("\n 1. Ввести значення всіх комірок самостійно ");

    X_cursor == 1 ? PrintCLR(1) : PrintCLR(0);
    printf("\n 2. Автоматично згеренерувати за допомогою random() ");

    X_cursor == 2 ? PrintCLR(1) : PrintCLR(0);
    printf("\n 3. Формування у відповідності до математичного виразу x + 2");

    X_cursor == 3 ? PrintCLR(1) : PrintCLR(0);
    printf("\n 4. На основі значення індексу елементу");

    printf("\n\n\n\n\n");

    switch (getch()) { // /\- 72,\/- 80,<- 75,>-77, 13 - enter,1 - 49, 2 - 50;
      case 72:
        if (X_cursor > 0) {
          X_cursor --;
        }
        break;
      case 80:
        if (X_cursor < 4) {
          X_cursor ++;
        }
        break;
      case 13:
      case 77:
        *variant = X_cursor + 1;
        Loop = 0;
        break;
    }
  }

  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | 0);
  system("cls");
}
void ScanVallOffMatrix(int MbMatrix, int** matr, int countRow, int countColumn) {
  int Loop = 1;
  int X_Cursor = 0;
  int Y_Cursor = 0;
  int NB_Press = 0;
  int N_C = 1;

  while (Loop) {
    PrintCLR(0);
    printf("\n ");
    system("cls");

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14 | 14);
    if (MbMatrix == 1) {
      printf("\nЗаповніть матрицю A:  \n\n");
    } else {
      printf("\nЗаповніть матрицю B:  \n\n");
    }
    for (int i = 0; i < countRow; i++) {
      for (int j = 0; j < countColumn; j++) {
        if (Y_Cursor == i && X_Cursor == j) {
          PrintCLR(1);
        } else {
          PrintCLR(0);
        }
        printf("%d  ", matr[i][j]);
        Tabulation(matr[i][j]);
      }
      printf("\n");
    }
    int getch_vall = getch();
    switch (getch_vall) { // /\- 72,\/- 80,<- 75,>-77, 13 - enter,1 - 49, 2 - 50; стерти - 8
      case 72:
        if (Y_Cursor > 0) {
          Y_Cursor --;
        }
        N_C = 1;
        break;
      case 80:
        if (countRow - 1 > Y_Cursor) {
          Y_Cursor ++;
        }
        N_C = 1;
        break;
      case 75:
        if (X_Cursor > 0) {
          X_Cursor --;
        }
        N_C = 1;
        break;
      case 77:
        if (countColumn - 1 > X_Cursor) {
          X_Cursor ++;
        }
        N_C = 1;
        break;
      case 13:
        Loop = 0;
        break;
      case 8:
        matr[Y_Cursor][X_Cursor] -= discharge(matr[Y_Cursor][X_Cursor], N_C) * N_C;
        if (N_C != 1) {
          N_C /= 10;
        }
        break;
      default:
        if (getch_vall >= 48 && getch_vall <= 57) {
          NB_Press = 9 - (57 - getch_vall);
          matr[Y_Cursor][X_Cursor] += NB_Press * N_C;
          N_C *= 10;
        }
        break;
    }
  }

  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | 0);
  system("cls");
}
void Tabulation(int value) {
  int cnt = 1;
  for (byte i = 8; i > 0; i--) {
    cnt *= 10;
    if (value > cnt) {
    } else {
      printf(" ");
    }
  }
}
int discharge(int Value, int Discharge) {
  return ((Value % (Discharge * 10)) / Discharge);
}
void PrintMatrixContent(int MbMatrix, int** matr, int countRow, int countColumn) {
  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 14 | 14);
  if (MbMatrix == 1) {
    printf("\n Матриця A:  \n");
  }
  if (MbMatrix == 2) {
    printf("\n Матриця B:  \n");
  }
  if (MbMatrix == 3) {
    printf("\n Транспортування матриці B:  \n");
  }
  if (MbMatrix == 4) {
    printf("\n Матриця AB:  \n");
  }

  PrintCLR(0);
  for (int i = 0; i < countRow; i++) {
    for (int j = 0; j < countColumn; j++) {
      printf("%d  ", matr[i][j]);
      Tabulation(matr[i][j]);
    }
    printf("\n");
  }
  SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), 15 | 0);
}
void transpose(int*** matr, int countRow, int countColumn) {
  int **tmp = NULL, p, Nb , Mb;
  tmp = (int **)calloc(countColumn, sizeof(int*));
  for (int i = 0; i < countColumn; i++) {
    tmp[i] = (int *)calloc(countRow, sizeof(int));
  }

  for (int i = 0; i < countColumn; i++) {
    for (int j = 0; j < countRow; j++) {
      tmp[i][j] = *matr[j][i];
    }
  }
  for (int i = 0; i < countRow; i++)free(*matr[i]);
  free(*matr);

  *matr = (int **)calloc(countColumn, sizeof(int*));
  for (int i = 0; i < countColumn; i++)*matr[i] = (int *)calloc(countRow, sizeof(int));

  printf("\n\n Транспонована матриця B[%d][%d]:\n ", countColumn, countRow);
  for (int i = 0; i < countColumn; i++) {
    printf("\n ");
    for (int j = 0; j < countRow; j++) {
      *matr[i][j] = tmp[i][j];
      printf("%3d ", *matr[i][j]);
    }
  }
  printf("\n ");

  p = Nb; Nb = Mb; Mb = p;

  for (int i = 0; i < countColumn; i++)free(tmp[i]);
  free(tmp);
}
int** MatrixMul(int** matr1, int** matr2, int countRow1, int countRow2, int countColumn1, int countColumn2) {
  int i, j, k;
  int** tmp = NULL;
  memAlloc(&tmp, countRow1, countColumn2);
  if (countColumn1 != countRow2) {
    printf("Error");
    exit(0);
  }
  else {
    for (i = 0; i < countRow1; i++)
      for (j = 0; j < countColumn2; j++)
      {
        tmp[i][j] = 0;
        for (k = 0; k < countRow2; k++)
          tmp[i][j] += matr1[i][k] * matr2[k][j];
      }

    return tmp;
  }
}

```
