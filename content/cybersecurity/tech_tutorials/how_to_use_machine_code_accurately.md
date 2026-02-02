---
title: "기계어를 정확히 사용하기"
draft: false
description: "정확히 기계어를 사용하는 방법"
tags: ["korean"]
categories: ["reversing"]
date: 2026-02-02 10:00:00+0900
---
## Assembly를 패치하기 위해 어떻게 기계어를 정확히 사용할 수 있는가?

- **인텔 기계어의 기본적인 구조**

![Intel Instruction Format](how_to_use_machine_code_accurately/Untitled.png)

  - **Instruction Prefixes**: Group 1~4로 구분되며, 각각 사용가능한 prefix 코드가 존재함
  - **Opcode**: 명령어
  - **ModR/M**: Opcode 명령어 뒤에 나오는 Operand가 어떤 유형인지 정의한다
    - **Mod**: 8개 레지스터와 24개 addressing mode를 정의
      - `00`: Displacement가 없는 메모리 모드
      - `01`: 8bit Displacement(+disp8) 메모리 모드
      - `10`: 32bit Displacement(+disp32) 메모리 모드
      - `11`: 레지스터 모드
    - **Reg/Opcode**: 레지스터 수 또는 3비트 이상의 Opcode 정보를 명시
    - **R/M**: 레지스터를 operand로 명시하거나 addressing mode를 인코딩하기 위해 Mod 필드와 결합해서 동작함
  - **SIB**
    - **scale**: scale factor를 정의
    - **index**: index 레지스터의 레지스터 숫자를 정의
    - **base**: base 레지스터의 레지스터 숫자를 정의
  - **Displacement**: 명령어가 immediate operand를 사용할 경우, operand는 항상 displacement bytes를 사용함(1,2,4바이트)
  - **Immediate**: 명령어가 immediate operand를 사용할 경우, operand는 항상 displacement bytes를 사용함(1,2,4바이트)

---

## MOV 명령어 예제

![MOV Instruction Table](how_to_use_machine_code_accurately/Untitled_1.png)

위의 MOV 테이블을 참고했을때, 만약 `mov ebx, 255`의 instruction을 수행하려면, **opcode는 B8+ rd id를 사용해야 한다.**

주의할 점으로, `1011 1000`(B8)은 EAX 기준이기 때문에, EBX에 대한 mov 명령어는 `1011 1011`(BB)가 된다.

> EBX는 EAX로부터 +3을 적용해야 한다. 왜냐하면, 레지스터는 EAX → ECX → EDX → EBX 순이기 때문이다.

위의 테이블을 단순 참조하지 않고 조금 더 상세하게 내부 구조를 보면, register에 immediate 값을 넣는 instruction format은 아래와 같다.

```
| 1011 W REG | DATA1 | DATA2 | DATA3 | DATA4 | → 5Bytes
```

- **W**: REG의 레지스터 유형(W=1, 32bit register)

예를 들어 EAX는 32bit 레지스터(W=1)이고, 첫번째 레지스터(REG=000)이므로 Opcode는 `1011 1000` = `B8`이 된다.

우리가 타겟으로 하는 EBX의 경우, 32bit 레지스터(W=1)이고, 세번째 레지스터(REG=011)이므로 Opcode는 `1011 1011`(`BB`)가 된다.

![Register Encoding](how_to_use_machine_code_accurately/Untitled_2.png)

따라서, `mov ebx, 0xFF`는 다음과 같이 표현한다.

```
10111011 11111111 00000000 00000000 00000000 (BB FF 00 00 00)
```

4바이트로 채워줘야 하는 이유는 operand를 4바이트 쓰기로 W=1에서 정의했기 때문이다.(`1011`**`1`**`011`)

참고로 operand는 **little endian**이므로, `0x01020304`를 넣으려면 `10111011 00000100 00000011 00000010 00000001` (`BB 04 03 02 01`)로 넣어야 한다.

---

## (추가) MOV r/m32, r32 형식

`mov r/m32, r32` 또는 `mov r32, r/m32`와 같은 경우 instruction format은 아래와 같다.

```
| 100010 D W | Mod REG R/M | Disp1 | Disp2 | Disp3 | Disp4 | → 6 Bytes
```

- **D**: REG가 가리키는 레지스터의 방향
  - D=0: 레지스터로부터 데이터를 읽음
  - D=1: 레지스터에 값을 넣음

![D Flag](how_to_use_machine_code_accurately/Untitled_3.png)

- **W**: 레지스터의 형태
  - W=0: 8bit register
  - W=1: 32bit register

![W Flag](how_to_use_machine_code_accurately/Untitled_4.png)

---

## 참고 자료

- [[asm] IA-32 MOV 명령어 해석 [1]](https://appleii.tistory.com/36)
