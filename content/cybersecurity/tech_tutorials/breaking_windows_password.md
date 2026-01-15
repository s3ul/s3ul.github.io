---
title: "Breaking Windows Password"
# weight: 2
draft: true
description: "How to bypass the Windows login screen on a local machine"
tags: ["windows", "korean"]
# series_order: 
categories: ["forensics"]
date: 2026-01-15 00:00:00+0900
---

1. VMware Workstation에서 윈도우즈 설치 이미지(iso)를 넣고, 재부팅하여 설치화면으로 이동한다.
    > 💡 부팅설정에 문제가 있다면 `Power On to Firmware`로 실행하여 부팅설정 순서를 변경한다.
2. 윈도우즈 설치화면에서 명령 프롬프트를 실행(Shift + F10)
3. C:\Windows\system32\Utilman.exe를 백업, cmd.exe의 이름을 Utilman.exe로 변경한다.
   > sethc.exe(Narrator)도 가능하며, sethc.exe로 진행할 경우 단계 5에서 지구본이 아니라 `Shift`를 5번 연속으로 입력해서 sethc.exe를 호출하여야 한다.
    ```plaintext
    move c:\Windows\System32\Utilman.exe c:\Windows\System32\Utilman.exe.bak
    copy c:\Windows\System32\cmd.exe c:\Windows\System32\Utilman.exe
    ```
    > 💡 C 드라이브를 찾지 못하는 경우 아래와 같이 드라이브 레터를 할당할 수 있다.

    ![Assign Drive Letter](breaking_windows_password/f62e5ecd1e80497093f9e138f617562d.png)
4. 재부팅  
    ```plaintext
    wpeutil reboot
    ```
5. 윈도우 로그인 창에서 '지구본' 아이콘을 선택하면 원래의 Utilman.exe 대신 변조된 cmd.exe가 실행되며, `net user {계정명} {password}`를 이용해 암호를 변경할 수 있다.
    ![Utilman](breaking_windows_password/12f7b3c4b428ca59919ea1f6ac35d925.png)
    > 오른쪽 하단 화살표 모양 아이콘을 클릭
    
- 위의 net user 명령어가 미상의 원인으로 실행되지 않는 경우 안전모드로 들어가주어야 한다.
    1. cmd.exe에서 msconfig를 입력, 안전모드를 설정하는 방법
    2. cmd.exe에서 bcdedit /set {default} safeboot minimal를 입력 **← 유효**
- 만약 로그인 화면의 계정목록에 타겟 계정이 없는 경우 다른 계정(관리자 권한)으로 로그인한 이후 레지스트리를 수정한다. 변경이 안되면 `regedit`으로 직접 수정
    ```plaintext
    reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\SpecialAccounts\UserList" /v {account name} /t REG_DWORD /d 1 /f
    ```