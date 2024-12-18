No **MIPS**, as instruções são classificadas em **três tipos principais** com base em seu formato:

---

### 1. **Tipo R (Register)**
As instruções do tipo R envolvem **apenas registradores**. Elas são usadas para operações aritméticas, lógicas e de deslocamento.

- **Formato**:  
   ```
   opcode | rs | rt | rd | shamt | funct
   ```
   - **opcode**: Código da operação (6 bits, geralmente `000000` para tipo R).  
   - **rs**: Registrador-fonte (5 bits).  
   - **rt**: Segundo registrador-fonte (5 bits).  
   - **rd**: Registrador-destino (5 bits, onde o resultado é armazenado).  
   - **shamt**: Valor de deslocamento (5 bits, usado em instruções de shift).  
   - **funct**: Função específica da operação (6 bits, detalha a operação).  

- **Exemplos de instruções do tipo R**:  
   - `add $rd, $rs, $rt` → Soma os valores em `rs` e `rt` e armazena em `rd`.  
   - `sub $rd, $rs, $rt` → Subtrai o valor em `rt` de `rs` e armazena em `rd`.  
   - `and $rd, $rs, $rt` → Operação lógica AND entre `rs` e `rt`, resultado em `rd`.  
   - `sll $rd, $rt, shamt` → Deslocamento lógico à esquerda.  

---

### 2. **Tipo I (Immediate)**  
As instruções do tipo I utilizam **um valor imediato** ou um deslocamento, além de registradores. Elas são geralmente usadas para operações que envolvem constantes ou acesso à memória.

- **Formato**:  
   ```
   opcode | rs | rt | imm
   ```
   - **opcode**: Código da operação (6 bits).  
   - **rs**: Registrador-fonte (5 bits).  
   - **rt**: Registrador-destino (5 bits).  
   - **imm**: Valor imediato ou deslocamento (16 bits, pode ser estendido com sinal).  

- **Exemplos de instruções do tipo I**:  
   - `addi $rt, $rs, imm` → Soma o valor imediato `imm` ao conteúdo de `rs` e armazena em `rt`.  
   - `lw $rt, offset($rs)` → Carrega um valor da memória para `rt` (load word).  
   - `sw $rt, offset($rs)` → Armazena o valor de `rt` na memória (store word).  
   - `beq $rs, $rt, label` → Salta para `label` se `rs` for igual a `rt` (branch equal).  
   - `bne $rs, $rt, label` → Salta para `label` se `rs` for diferente de `rt`.  

---

### 3. **Tipo J (Jump)**  
As instruções do tipo J são usadas para realizar **saltos** dentro do programa, sem condições.

- **Formato**:  
   ```
   opcode | addr
   ```
   - **opcode**: Código da operação (6 bits).  
   - **addr**: Endereço de destino (26 bits).  

- **Exemplos de instruções do tipo J**:  
   - `j label` → Salta para o endereço definido pelo rótulo `label`.  
   - `jal label` → Salta para `label` e armazena o endereço de retorno em `$ra` (Jump and Link, usado em chamadas de função).

---

### Resumo dos tipos
| **Tipo** | **Formato**               | **Uso**                                  | **Exemplo**         |
|----------|---------------------------|-----------------------------------------|---------------------|
| **R**    | opcode | rs | rt | rd | shamt | funct | Operações com registradores.        | `add $t0, $t1, $t2` |
| **I**    | opcode | rs | rt | imm | Constantes e operações de memória.      | `lw $t0, 4($t1)`    |
| **J**    | opcode | addr | Saltos incondicionais.                  | `j 10000`           |

---

# Como escrever código de máquina MIPS

Para transformar **código de software** (escrito em uma linguagem de montagem MIPS) em **código de máquina MIPS**, é necessário seguir algumas etapas que envolvem o funcionamento do **assembler** e a representação binária das instruções. Abaixo está um guia passo a passo:

---

### **1. Escrever o código em Assembly MIPS**  
Primeiro, o código é escrito em **linguagem assembly**, que é mais próxima do hardware, mas ainda utilizável por humanos.

**Exemplo de código em assembly MIPS**:  
```assembly
add $t0, $t1, $t2    # Soma o conteúdo de $t1 e $t2 e armazena em $t0
```

---

### **2. Identificar o tipo de instrução (R, I ou J)**  
Cada instrução no MIPS pertence a um dos **três tipos**:
- **Tipo R**: Operações com registradores (e.g., `add`, `sub`).
- **Tipo I**: Operações com valores imediatos ou acesso à memória (e.g., `addi`, `lw`).
- **Tipo J**: Instruções de salto (e.g., `j`, `jal`).

No caso da instrução **`add`**, ela é **do tipo R**.

---

### **3. Mapear a instrução para o formato binário**
Para cada tipo de instrução, existe um formato fixo em binário que deve ser preenchido com os valores corretos.

#### **Formato para instruções do tipo R**:  
```
opcode (6 bits) | rs (5 bits) | rt (5 bits) | rd (5 bits) | shamt (5 bits) | funct (6 bits)
```

- **opcode**: Código da operação. Para o tipo R, o opcode é `000000`.  
- **rs**: Registrador-fonte 1.  
- **rt**: Registrador-fonte 2.  
- **rd**: Registrador-destino (onde o resultado será armazenado).  
- **shamt**: Valor de deslocamento (usado apenas em instruções de shift). Neste caso, `0`.  
- **funct**: Identificador específico da operação. Para `add`, o funct é `100000`.  

---

### **4. Converter os registradores em números**  
Os registradores MIPS têm números associados, que são usados no código binário:

| Registrador | Número | Binário (5 bits) |
|-------------|--------|-----------------|
| `$t0`       | 8      | 01000           |
| `$t1`       | 9      | 01001           |
| `$t2`       | 10     | 01010           |

---

### **5. Montar o código binário**
Agora, substituímos os campos no formato da instrução com os valores correspondentes.

Para **`add $t0, $t1, $t2`**, temos:  
- **opcode**: `000000`  
- **rs**: `$t1` → `01001`  
- **rt**: `$t2` → `01010`  
- **rd**: `$t0` → `01000`  
- **shamt**: `00000` (não usado)  
- **funct**: `100000`  

Substituindo no formato R:  
```
000000 | 01001 | 01010 | 01000 | 00000 | 100000
```

---

### **6. Converter para hexadecimal**
O código binário completo pode ser agrupado em blocos de **4 bits** e convertido para hexadecimal.  
1. Agrupando os bits:  
   ```
   000000 01001 01010 01000 00000 100000
   ```
2. Separando em blocos de 4 bits:  
   ```
   0000 0001 0010 1010 0100 0000 0010 0000
   ```
3. Convertendo para hexadecimal:  
   ```
   0x012A4020
   ```

---

### **7. Gerar o código de máquina**
O código de máquina é a **representação hexadecimal** da instrução, que pode ser interpretada diretamente pelo processador MIPS.

Para a instrução **`add $t0, $t1, $t2`**, o código de máquina é:  
```
0x012A4020
```

---

### **Resumo das etapas**  
1. Escreva o código assembly MIPS.  
2. Identifique o tipo da instrução (R, I ou J).  
3. Substitua os campos do formato (opcode, registradores, imediato, funct).  
4. Converta os valores para binário.  
5. Combine tudo em um código binário de 32 bits.  
6. Converta o binário para hexadecimal.  
7. Obtenha o código de máquina.

---
<details>
<summary>Exemplo completo com instrução do tipo I</summary>


## **1. Instrução do tipo R**

### Exemplo 1: `sub $s0, $s1, $s2`
- **Descrição**: Subtrai o conteúdo de `$s2` do conteúdo de `$s1` e armazena o resultado em `$s0`.  
- **Formato**: Tipo R → `opcode | rs | rt | rd | shamt | funct`  

**Valores:**
- **opcode**: `000000` (fixo para instruções tipo R)  
- **rs**: `$s1` → `10001`  
- **rt**: `$s2` → `10010`  
- **rd**: `$s0` → `10000`  
- **shamt**: `00000` (não usado)  
- **funct**: `100010` (código para `sub`)  

**Binário**:  
```
000000 10001 10010 10000 00000 100010
```

**Hexadecimal**:  
- Agrupando em blocos de 4 bits:  
  ```
  0000 0010 0011 0010 1000 0000 0010 0010
  ```
- Conversão:  
  ```
  0x02328022
  ```

**Código de máquina**: `0x02328022`  

---

## **2. Instrução do tipo I**

### Exemplo 2: `lw $t0, 8($t1)`
- **Descrição**: Carrega um valor da memória no endereço **`$t1 + 8`** para o registrador `$t0`.  
- **Formato**: Tipo I → `opcode | rs | rt | imm`  

**Valores:**
- **opcode**: `100011` (para `lw`)  
- **rs**: `$t1` → `01001`  
- **rt**: `$t0` → `01000`  
- **imm**: `8` → `0000 0000 0000 1000`  

**Binário**:  
```
100011 01001 01000 0000 0000 0000 1000
```

**Hexadecimal**:  
- Agrupando em blocos de 4 bits:  
  ```
  1000 1101 0010 1000 0000 0000 0000 1000
  ```
- Conversão:  
  ```
  0x8D280008
  ```

**Código de máquina**: `0x8D280008`

---

### Exemplo 3: `addi $t2, $t3, 15`
- **Descrição**: Adiciona o valor imediato `15` ao conteúdo de `$t3` e armazena o resultado em `$t2`.  
- **Formato**: Tipo I → `opcode | rs | rt | imm`  

**Valores:**
- **opcode**: `001000` (para `addi`)  
- **rs**: `$t3` → `01011`  
- **rt**: `$t2` → `01010`  
- **imm**: `15` → `0000 0000 0000 1111`  

**Binário**:  
```
001000 01011 01010 0000 0000 0000 1111
```

**Hexadecimal**:  
- Agrupando em blocos de 4 bits:  
  ```
  0010 0001 0110 1010 0000 0000 0000 1111
  ```
- Conversão:  
  ```
  0x215A000F
  ```

**Código de máquina**: `0x215A000F`

---

### Exemplo 4: `addi $t0, $t1, 5`
- **Descrição**: Adiciona o valor imediato `5` ao conteúdo de `$t1` e armazena o resultado em `$t0`.  
- **Formato**: Tipo I → `opcode | rs | rt | imm`  

**Valores:**
- **opcode**: `001000` (para `addi`)  
- **rs**: `$t1` → `01001`  
- **rt**: `$t0` → `01000`  
- **imm**: `5` → `0000 0000 0000 0101`  

**Binário**:  
```
001000 01001 01000 0000 0000 0000 0101
```

**Hexadecimal**:  
```
0x21280005
```

**Código de máquina**: `0x21280005`

---

## **3. Instrução do tipo J**

### Exemplo 5: `j 0x00400020`
- **Descrição**: Salta para o endereço `0x00400020`.  
- **Formato**: Tipo J → `opcode | addr`  

**Valores:**
- **opcode**: `000010` (para `j`)  
- **addr**: O endereço `0x00400020` deve ser ajustado para um valor de **26 bits**.  
   - Endereço original: `0x00400020`  
   - Remove os 2 últimos bits (endereçamento em palavras): `0001 0000 0000 0000 0000 1000` (26 bits).  

**Binário**:  
```
000010 0001 0000 0000 0000 0000 1000
```

**Hexadecimal**:  
- Agrupando em blocos de 4 bits:  
  ```
  0000 1000 0100 0000 0000 0000 0010 0000
  ```
- Conversão:  
  ```
  0x08100020
  ```

**Código de máquina**: `0x08100020`

---

### Exemplo 5: `jal 0x00400100`
- **Descrição**: Salta para o endereço `0x00400100` e salva o retorno no registrador `$ra`.  
- **Formato**: Tipo J → `opcode | addr`  

**Valores:**
- **opcode**: `000011` (para `jal`)  
- **addr**: Remove os dois últimos bits do endereço `0x00400100`:  
   ```
   0001 0000 0000 0000 0100
   ```

**Binário**:  
```
000011 0001 0000 0000 0000 0000 0100
```

**Hexadecimal**:  
- Agrupando:  
  ```
  0000 1100 0100 0000 0000 0000 0001 0000
  ```
- Conversão:  
  ```
  0x0C100010
  ```

**Código de máquina**: `0x0C100010`

---

## **Resumo dos Exemplos**

| **Instrução**         | **Tipo** | **Binário**                              | **Hexadecimal** |
|------------------------|----------|-----------------------------------------|-----------------|
| `sub $s0, $s1, $s2`   | R        | `000000 10001 10010 10000 00000 100010` | `0x02328022`    |
| `lw $t0, 8($t1)`      | I        | `100011 01001 01000 0000 0000 0000 1000`| `0x8D280008`    |
| `addi $t2, $t3, 15`   | I        | `001000 01011 01010 0000 0000 0000 1111`| `0x215A000F`    |
| `j 0x00400020`        | J        | `000010 0001 0000 0000 0000 0000 1000` | `0x08100020`    |
| `jal 0x00400100`      | J        | `000011 0001 0000 0000 0000 0000 0100` | `0x0C100010`    |

---
</details>
