### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Felkészülés leküldéses telepítésre Linux-kiszolgálón

1. Győződjön meg arról, hogy nincs-e hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló között.
1. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. Használja ezt a fiókot, csak a leküldéses telepítéshez és frissítésekhez.
1. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
1. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
1. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
1. Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be **gyökér** szintű felhasználóként.

    b. Az a **/etc/ssh/sshd_config** fájlt és keresse meg azt a sort, kezdődik **PasswordAuthentication**.

    c. Állítsa vissza a sort, és módosítsa az értéket **Igen**.

    d. Keresse meg a sort, amely a következővel kezdődik: **alrendszer**, és állítsa vissza a sort.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Indítsa újra az **sshd** szolgáltatást.

1. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be a konfigurációs kiszolgálóra.

    b. Nyissa meg a következőt: **cspsconfigtool.exe**. Parancsikonja az asztalon, és a %ProgramData%\home\svsystems\bin mappában érhető el.

    c. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.

    d. Adja hozzá a létrehozott fiókot.

    d. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
