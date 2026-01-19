A continuación tienes **programas C independientes** (un archivo por operación). Quité los menos útiles / poco “C puro” (encode, hash, id, magic methods, etc.). Todos usan el mismo `texto` base: `"Hola mundo 23 de abril del 2026"`.

---

```c
/* 01_len.c */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *texto = "Hola mundo 23 de abril del 2026";
    printf("%zu\n", strlen(texto));
    return 0;
}
```

```c
/* 02_upper.c */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *texto = "Hola mundo 23 de abril del 2026";
    for (int i=0; texto[i]; i++) putchar(toupper((unsigned char)texto[i]));
    putchar('\n');
    return 0;
}
```

```c
/* 03_lower.c */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *texto = "Hola mundo 23 de abril del 2026";
    for (int i=0; texto[i]; i++) putchar(tolower((unsigned char)texto[i]));
    putchar('\n');
    return 0;
}
```

```c
/* 04_capitalize.c  (primera letra en mayúscula; resto igual) */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    int done = 0;
    for(int i=0; t[i]; i++){
        unsigned char c = (unsigned char)t[i];
        if(!done && isalpha(c)) { putchar(toupper(c)); done = 1; }
        else putchar(c);
    }
    putchar('\n');
    return 0;
}
```

```c
/* 05_title.c  (primera letra de cada palabra en mayúscula; resto en minúscula) */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    int new_word = 1;
    for(int i=0; t[i]; i++){
        unsigned char c = (unsigned char)t[i];
        if (isspace(c)) { new_word = 1; putchar(c); }
        else if (isalpha(c)) {
            putchar(new_word ? toupper(c) : tolower(c));
            new_word = 0;
        } else { putchar(c); new_word = 0; }
    }
    putchar('\n');
    return 0;
}
```

```c
/* 06_swapcase.c */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    for(int i=0; t[i]; i++){
        unsigned char c = (unsigned char)t[i];
        if (islower(c)) putchar(toupper(c));
        else if (isupper(c)) putchar(tolower(c));
        else putchar(c);
    }
    putchar('\n');
    return 0;
}
```

```c
/* 07_trim.c  (strip: quita espacios al inicio y al final) */
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int main(void){
    char s[] = "   Hola mundo 23 de abril del 2026   ";
    size_t n = strlen(s);
    size_t i = 0; while (i < n && isspace((unsigned char)s[i])) i++;
    size_t j = n; while (j > i && isspace((unsigned char)s[j-1])) j--;
    for (size_t k=i; k<j; k++) putchar(s[k]);
    putchar('\n');
    return 0;
}
```

```c
/* 08_lstrip.c */
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int main(void){
    char s[] = "   Hola mundo 23 de abril del 2026";
    size_t i=0; while (s[i] && isspace((unsigned char)s[i])) i++;
    puts(s+i);
    return 0;
}
```

```c
/* 09_rstrip.c */
#include <stdio.h>
#include <string.h>
#include <ctype.h>

int main(void){
    char s[] = "Hola mundo 23 de abril del 2026   ";
    size_t n = strlen(s);
    while (n>0 && isspace((unsigned char)s[n-1])) n--;
    fwrite(s, 1, n, stdout);
    putchar('\n');
    return 0;
}
```

```c
/* 10_replace_char.c  (replace("o","a")) */
#include <stdio.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    for(int i=0; t[i]; i++) putchar(t[i]=='o' ? 'a' : t[i]);
    putchar('\n');
    return 0;
}
```

```c
/* 11_replace_substring.c  (replace("Hola","Adios") SOLO primera ocurrencia) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    const char *from = "Hola", *to = "Adios";
    const char *p = strstr(t, from);

    if (!p) { puts(t); return 0; }

    fwrite(t, 1, (size_t)(p - t), stdout);
    fputs(to, stdout);
    fputs(p + strlen(from), stdout);
    putchar('\n');
    return 0;
}
```

```c
/* 12_split_space.c  (split por espacios: imprime tokens línea por línea) */
#include <stdio.h>
#include <string.h>

int main(void){
    char s[] = "Hola mundo 23 de abril del 2026";
    for(char *tok = strtok(s, " "); tok; tok = strtok(NULL, " "))
        puts(tok);
    return 0;
}
```

```c
/* 13_split_char_o.c  (split("o"): imprime partes) */
#include <stdio.h>
#include <string.h>

int main(void){
    char s[] = "Hola mundo 23 de abril del 2026";
    for(char *tok = strtok(s, "o"); tok; tok = strtok(NULL, "o"))
        puts(tok);
    return 0;
}
```

```c
/* 14_find_first.c  (find("m")) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    const char *p = strchr(t, 'm');
    printf("%ld\n", p ? (long)(p - t) : -1L);
    return 0;
}
```

```c
/* 15_find_last.c  (rfind("o")) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    const char *p = strrchr(t, 'o');
    printf("%ld\n", p ? (long)(p - t) : -1L);
    return 0;
}
```

```c
/* 16_count_char.c  (count("o")) */
#include <stdio.h>

int main(void){
    const char *t = "Hola mundo 23 de abril del 2026";
    int c = 0;
    for(int i=0; t[i]; i++) if (t[i]=='o') c++;
    printf("%d\n", c);
    return 0;
}
```

```c
/* 17_startswith.c  (startswith("Hola")) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    const char *pref="Hola";
    printf("%d\n", strncmp(t, pref, strlen(pref))==0);
    return 0;
}
```

```c
/* 18_endswith.c  (endswith("mundo")) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    const char *suf="mundo";
    size_t lt=strlen(t), ls=strlen(suf);
    int ok = (lt>=ls) && (strcmp(t+(lt-ls), suf)==0);
    printf("%d\n", ok);
    return 0;
}
```

```c
/* 19_indexing.c  (texto[0], texto[-1] equivalentes) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    printf("t[0]=%c\n", t[0]);
    printf("t[last]=%c\n", t[strlen(t)-1]);
    return 0;
}
```

```c
/* 20_slice_0_4.c  (texto[0:4]) */
#include <stdio.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    for(int i=0; i<4 && t[i]; i++) putchar(t[i]);
    putchar('\n');
    return 0;
}
```

```c
/* 21_slice_from_5.c  (texto[5:]) */
#include <stdio.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    puts(t+5);
    return 0;
}
```

```c
/* 22_reverse.c  (texto[::-1]) */
#include <stdio.h>
#include <string.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    for(int i=(int)strlen(t)-1; i>=0; i--) putchar(t[i]);
    putchar('\n');
    return 0;
}
```

```c
/* 23_concat_exclamation.c  (texto + "!") */
#include <stdio.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    printf("%s!\n", t);
    return 0;
}
```

```c
/* 24_repeat_2.c  (texto * 2) */
#include <stdio.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    printf("%s%s\n", t, t);
    return 0;
}
```

```c
/* 25_replace_spaces_underscore.c  (replace(" ","_")) */
#include <stdio.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    for(int i=0; t[i]; i++) putchar(t[i]==' ' ? '_' : t[i]);
    putchar('\n');
    return 0;
}
```

```c
/* 26_get_numbers.c  (obtener todos los números) */
#include <stdio.h>
#include <ctype.h>

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    for(int i=0; t[i]; i++)
        if (isdigit((unsigned char)t[i])) putchar(t[i]);
    putchar('\n');
    return 0;
}
```

```c
/* 27_get_vowels.c  (obtener todas las vocales) */
#include <stdio.h>
#include <ctype.h>

int is_vowel(unsigned char c){
    c = (unsigned char)tolower(c);
    return c=='a'||c=='e'||c=='i'||c=='o'||c=='u';
}

int main(void){
    const char *t="Hola mundo 23 de abril del 2026";
    for(int i=0; t[i]; i++){
        unsigned char c = (unsigned char)t[i];
        if (is_vowel(c)) putchar(t[i]);
    }
    putchar('\n');
    return 0;
}
```

---

Si quieres, puedo generar un “pack” más grande (más operaciones), pero en C varias funciones de Python **no tienen equivalente directo** y se vuelven demasiado largas para un ejemplo básico.
