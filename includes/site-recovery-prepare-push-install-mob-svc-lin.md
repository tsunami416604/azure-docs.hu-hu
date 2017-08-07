### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Felkészülés leküldéses telepítésre Linux-kiszolgálón

1. Gondoskodjon róla, hogy legyen hálózati kapcsolat a Linux-számítógép és a folyamatkiszolgáló közt.
2. Hozzon létre egy fiókot, amelyen keresztül a folyamatkiszolgáló hozzáférhet a számítógéphez. A fióknak **gyökér** szintű felhasználónak kell lennie a forrás Linux-kiszolgálón. (Kizárólag a leküldéses telepítéshez és frissítésekhez használja ezt a fiókot.)
3. Ellenőrizze, hogy a forrás Linux-kiszolgálón található /etc/hosts fájl tartalmaz-e olyan bejegyzéseket, amelyek a helyi gazdanevet az összes hálózati adapterhez társított IP-címekké képezik le.
4. Telepítse a legfrissebb openssh, openssh-server és openssl csomagokat a replikálni kívánt számítógépen.
5. Ügyeljen arra, hogy a Secure Shell (SSH) engedélyezve legyen, és a 22-es porton fusson.
6. Engedélyezze az SFTP alrendszer és a jelszó hitelesítését az sshd_config fájlban:
  1.  Jelentkezzen be **gyökér** szintű felhasználóként.
  2.  Az /etc/ssh/sshd_config fájlban keresse meg azt a sort, amelynek a kezdete **PasswordAuthentication**.
  3.  Állítsa vissza a sort, és módosítsa az értéket **yes** értékre.
  4.  Keresse meg a sort, amelynek a kezdete **Subsystem**, és állítsa vissza.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)
  5. Indítsa újra az **sshd** szolgáltatást.

7. Adja hozzá a CSPSConfigtool eszközben létrehozott fiókot.
    1.  Jelentkezzen be a konfigurációs kiszolgálóra.
    2.  Nyissa meg a következőt: **cspsconfigtool.exe**. (A parancsikonja elérhető az asztalon, az alkalmazás pedig a %ProgramData%\home\svsystems\bin mappában található.)
    3.  A **Manage Accounts** (Fiókok kezelése) lapon kattintson az **Add Account** (Fiók hozzáadása) lehetőségre.
    4.  Adja hozzá a létrehozott fiókot. 
    5.  Adja meg a számítógépek replikációjának engedélyezése során használt hitelesítő adatokat.
