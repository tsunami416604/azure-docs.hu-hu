### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Felkészülés leküldéses telepítésre Linux-kiszolgálón

1. Győződjön meg arról, hogy van hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló között.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. Ezt a fiókot használják, csak az ügyfélleküldéses telepítés és a frissítéseket.
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
5. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
6. SFTP alrendszer és a jelszó-hitelesítés engedélyezése a sshd_config fájlban. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be **gyökér** szintű felhasználóként.

    b. Az a **/etc/ssh/sshd_config** fájlt, keresse meg a sor kezdődő **PasswordAuthentication**.

    c. Állítsa vissza a sort, és módosítsa az értéket a következőre **Igen**.

    d. A sor kezdődő található **alrendszer**, és állítsa vissza a sor.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Indítsa újra az **sshd** szolgáltatást.

7. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot. Kövesse az alábbi lépéseket:

    a. Jelentkezzen be a konfigurációs kiszolgálóra.

    b. Nyissa meg a következőt: **cspsconfigtool.exe**. Egy parancsikont az asztalon és az %ProgramData%\home\svsystems\bin mappában érhető el.

    c. Az a **fiókok kezelése** lapon jelölje be **fiók hozzáadása**.

    d. Adja hozzá a létrehozott fiókot.

    d. Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
