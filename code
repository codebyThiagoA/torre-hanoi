; Torre de Hanoi
; Alunos: Thiago Bernardo e Guilherme Barbosa.

section .data ;Declara dados estáticos ou globais que serão armazenados na memória do programa

    pergunta db 'Digite a quantidade de discos (1 a 9):', 0   ; string com a mensagem de pergunta ao usuário, terminada em 0
    movimento1 db 'Mova o disco ', 0                          
    movimento3 db ' da coluna ', 0                            
    movimento2 db ' até a coluna ', 0                         
    concluido db 'Concluído!', 0                             
    entrada_invalida db 'Entrada inválida. Digite um número de 1 a 9.', 0   ; mensagem exibida se o usuário digitar um número inválido
    coluna_origem db 'A', 0                                   ; nome da coluna de origem
    coluna_auxiliar db 'B', 0                                 ; nome da coluna auxiliar
    coluna_destino db 'C', 0                                  ; nome da coluna destino
    quebra_de_linha db 10                                     ; caractere de nova linha (\n)

section .bss                     ;reserva espaço na memória para variáveis não inicializadas.
    entrada resb 3               ; Buffer para armazenar a entrada do usuário (até 3 bytes)
    quant_disc resb 1            ; Quantidade de discos digitada pelo usuário
    len_buffer resb 2            ; Buffer auxiliar usado para converter números em string

section .text                    ;código executável
    global _start                

_start:
inicio:
    mov ecx, pergunta            ; imprime a pergunta ao usuário
    call printar_ecx_0

    mov ecx, entrada             ; define onde armazenar a entrada do usuário
    call ler_3_2                 ; lê até 2 caracteres do usuário

    call avaliar_entrada         ; valida se entrada está entre 1 e 9

    call converter_string_int    ; converte string ASCII para inteiro
    mov [quant_disc], dl         ; armazena valor convertido

    call torre_de_hanoi          ; executa algoritmo da Torre de Hanoi

    mov ecx, concluido           ; mensagem final
    call printar_ecx_0

    mov eax, 1                   ; syscall exit
    xor ebx, ebx                 ; código de saída 0
    int 0x80                     ; encerra programa

torre_de_hanoi:
    cmp byte [quant_disc], 1     ; se número de discos for 1
    je disco_unico               ; vai para o caso base
    jmp mais_discos              ; caso recursivo

disco_unico:
    mov ecx, movimento1
    call printar_ecx_0           ; imprime "Mova o disco "

    call printar_n_discos        ; imprime número do disco

    mov ecx, movimento3
    call printar_ecx_0           ; imprime " da coluna "

    mov ecx, coluna_origem
    call printar_ecx_0           ; imprime coluna origem

    mov ecx, movimento2
    call printar_ecx_0           ; imprime " até a coluna "

    mov ecx, coluna_destino
    call printar_ecx_0           ; imprime coluna destino

    mov ecx, quebra_de_linha
    call printar_ecx             ; imprime nova linha

    jmp fim                      ; retorna

mais_discos:
    dec byte [quant_disc]        ; reduz o número de discos
    push word [quant_disc]       ; salva o estado atual na pilha
    push word [coluna_origem]
    push word [coluna_auxiliar]
    push word [coluna_destino]

    ; troca auxiliar <-> destino temporariamente
    mov dx, [coluna_auxiliar]
    mov cx, [coluna_destino]
    mov [coluna_destino], dx
    mov [coluna_auxiliar], cx

    call torre_de_hanoi          ; chamada recursiva

    pop word [coluna_destino]    ; restaura estado
    pop word [coluna_auxiliar]
    pop word [coluna_origem]
    pop word [quant_disc]

    mov ecx, movimento1
    call printar_ecx_0           ; imprime "Mova o disco "

    inc byte [quant_disc]        ; incrementa temporariamente para mostrar disco
    call printar_n_discos
    dec byte [quant_disc]        ; volta ao estado anterior

    mov ecx, movimento3
    call printar_ecx_0           ; imprime " da coluna "

    mov ecx, coluna_origem
    call printar_ecx_0

    mov ecx, movimento2
    call printar_ecx_0

    mov ecx, coluna_destino
    call printar_ecx_0

    mov ecx, quebra_de_linha
    call printar_ecx

    ; troca origem <-> auxiliar para próxima chamada recursiva
    mov dx, [coluna_auxiliar]
    mov cx, [coluna_origem]
    mov [coluna_origem], dx
    mov [coluna_auxiliar], cx

    call torre_de_hanoi          ; chamada recursiva

fim:
    ret

converter_string_int:
    mov edx, entrada[0]          ; pega primeiro caractere
    sub edx, '0'                 ; converte ASCII para número
    mov eax, entrada[1]          
    cmp eax, 0x0a                ; verifica se tem apenas 1 dígito
    je um_algarismo
    sub eax, '0'                 ; converte segundo caractere
    imul edx, 10                 ; multiplicação do primeiro por 10
    add edx, eax                 ; soma os dois
um_algarismo:
    ret

printar_ecx:
    mov eax, 4                   ; syscall write
    mov ebx, 1                   ; saída padrão
    mov edx, 1                   ; escreve 1 byte
    int 0x80                     ; imprime byte apontado por ecx
    ret

printar_ecx_0:
printar_loop:
    mov al, [ecx]                ; lê caractere
    cmp al, 0                    ; fim da string?
    je printar_exit
    call printar_ecx             ; imprime caractere
    inc ecx                      ; avança ponteiro
    jmp printar_loop
printar_exit:
    ret

ler_3_2:
    mov eax, 3                   ; syscall read
    mov ebx, 0                   ; stdin
    mov edx, 2                   ; lê até 2 caracteres
    int 0x80                     ; realiza leitura
    ret

avaliar_entrada:
    mov al, entrada[0]           ; primeiro caractere
    cmp al, '1'
    jl invalido                  ; menor que 1?
    cmp al, '9'
    jg invalido                  ; maior que 9?

    mov dl, entrada[1]           ; segundo caractere
    cmp dl, 0x0a                 ; ENTER?
    je valido
    jmp invalido

invalido:
    mov ecx, entrada_invalida
    call printar_ecx_0           ; imprime mensagem de erro
    mov ecx, quebra_de_linha
    call printar_ecx
    jmp inicio                   ; recomeça

valido:
    ret

converter_int_string:
    dec edi                      ; move posição do buffer
    xor edx, edx                 ; limpa edx
    mov ecx, 10                  ; divisor = 10
    div ecx                      ; divide eax por 10
    add dl, '0'                  ; converte resto para ASCII
    mov [edi], dl                ; salva caractere no buffer
    test eax, eax                ; mais dígitos?
    jnz converter_int_string     ; continua convertendo
    ret

printar_n_discos:
    movzx eax, byte [quant_disc] ; carrega quant_disc com zero extend
    lea edi, [len_buffer + 2]    ; início do buffer para número
    call converter_int_string    ; converte número em string

    mov eax, 4                   ; syscall write
    mov ebx, 1                   ; stdout
    lea ecx, [edi]               ; ponteiro para início da string
    lea edx, [len_buffer + 2]
    sub edx, ecx                 ; calcula tamanho
    int 0x80                     ; imprime
    ret
