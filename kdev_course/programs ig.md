# hello world on bootloader

```asm
ORG 0x7c00  ; origin
BITS 16     ; specifying 16 bit memory addressing

start:
  mov si, message    ; load address of message
  call print         ; call print subroutine
  jmp $              ; keep jumping to current location and halt process flow

print:
  mov bx, 0          ; page number in the Int 10/AH=0Eh

.loop:
  lodsb              ; loads next byte from [SI] to AL and increements SI
  cmp al, 0         ; compares AL with null terminator 0
  je .done          ; if true then go return
  call print_char   ; call the print_char subroutine to print char in AL
  jmp .loop         ; repeat

.done:
  ret

print_char:
  mov ah, 0eh        ; sets up a BIOS interrupt 0x10 to display a character 
  int 0x10           ; in AL in teletype mode
  ret

message: db "hello world!", 0

times 510-($ - $$) db 0
dw 0xAA55
```

