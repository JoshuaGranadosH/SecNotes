## Linux

Usualmente el llamar directamente a la pila no retorna una shell, por lo que hay que buscar instrucciones de tipo `jmp` y `call`, pudiendo hacer un filtrado de estas instrucciones con `grep`. Ejemplo: `objdump -d [binario] | grep jmp`. Teniendo en cuenta que deberían apuntar a los ROP Gadgets ya sea de 32 o 64 bits respectivamente.

jmp: ff e0 (hexadecimal) *%rax
call: ff d0 (hexadecimal) *%rax

Sacar offset en gdb

Setear breakpoint antes de que termine el programa

info frame

x/24wx $rsp

p/d rip - rsp

### Debugging Local

#### Consideraciones

Para debuggear localmente un binario tener en cuenta la protección `ASLR`.
- Para deshabilitar: `echo 0 | sudo tee /proc/sys/kernel/randomize_va_space`
- Para habilitar: `echo 2 | sudo tee /proc/sys/kernel/randomize_va_space`
- Para deshabilitar permanentemente, es necesario configurarlo en `sysctl`. Agregar el archivo `/etc/sysctl.d/01-disable-aslr.conf` con el contenido: `kernel.randomize_va_space = 0`

## GDB

set disassembly-flavor intel

## Script de exploit base

## Tips n' tricks


```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char *argv[]) {
   char *ptr;

   if(argc < 3) {
      printf("Usage: %s <environment var> <target program name>\n", argv[0]);
      exit(0);
   }
   ptr = getenv(argv[1]); /* Get env var location. */
   ptr += (strlen(argv[0]) - strlen(argv[2]))*2; /* Adjust for program name. */
   printf("%s will be at %p\n", argv[1], ptr);
}
```