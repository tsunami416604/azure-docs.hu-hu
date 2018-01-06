## <a name="download-install-and-register-the-azure-backup-agent"></a>Letöltése, telepítése és regisztrálása az Azure Backup szolgáltatás ügynöke
Miután létrehozta a Recovery Services-tároló, telepítse a használatával az adatok biztonsági mentése az Azure Backup szolgáltatás ügynökének minden, a Windows gép (Windows Server, a Windows ügyfél, a System Center Data Protection Manager-kiszolgáló vagy Azure biztonsági mentési kiszolgálóként működő számítógép).

1. Nyissa meg az előfizetését a [Azure Portal](https://ms.portal.azure.com/).
2. A bal oldali menüben válassza **minden szolgáltatás** írja be a szolgáltatások listájában **Recovery Services**. Kattintson a **Recovery Services-tárolók**.

   ![Nyissa meg a Recovery Services-tároló](../articles/backup/media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)
3. A gyors kezdés lapon kattintson a **For Windows Server, a System Center Data Protection Manager vagy a Windows ügyfél** lehetőség alatt **ügynök letöltése**. Kattintson a **mentése** másolja azt a helyi számítógépen.
   
    ![Ügynök mentése](./media/backup-install-agent/agent.png)
4. Az ügynök telepítése után kattintson duplán a MARSAgentInstaller.exe az Azure Backup szolgáltatás ügynökének telepítésének indításakor. Válassza ki a telepítési mappa és az ügynök számára szükséges ideiglenes mappa. A gyorsítótármappa megadott helye szabad terület, a biztonsági mentési adatok legalább 5 %-át kell.
5. Ha csatlakozni az internethez, a proxykiszolgálót használni a **proxykonfigurációt** képernyőn, írja be a proxy-kiszolgáló adatait. Ha egy hitelesített proxykiszolgálót használ, adja meg a felhasználói nevet és jelszót adatokat ezen a képernyőn.
6. Az Azure Backup szolgáltatás ügynökének telepíti a .NET-keretrendszer 4.5 és Windows PowerShell (Ha még nem érhető el) a telepítés befejezéséhez.
7. Az ügynök telepítése után kattintson a **továbblépni a regisztrációra** gomb a munkafolyamat folytatásához.
   
   ![Regisztráljon](./media/backup-install-agent/register.png)
8. A tároló hitelesítő adatait képernyőn válassza ki a letöltött tárolói hitelesítő adatok fájlját.
   
    ![Tárolói hitelesítő adatokat](./media/backup-install-agent/vc.png)
   
    A tároló hitelesítési adatait tartalmazó fájlt csak esetén érvényes 48 óra (után a portálról letöltés). Ha az Azure portálra a képernyő (például "tárolói hitelesítő adatok fájlját megadott lejárt"), bejelentkezési hibákat tapasztal, és töltse le újból a tároló hitelesítési adatait tartalmazó fájlt.
   
    Győződjön meg arról, a telepítő alkalmazások hozzáférhetnek a tároló hitelesítési adatait tartalmazó fájlt. Hozzáféréssel kapcsolatos hibákat észlel, ha a tároló hitelesítési adatait tartalmazó fájlt a helyi számítógép egy ideiglenes helyre másolja, majd próbálja megismételni a műveletet.
   
    Ha például a "tárolói hitelesítő adatok érvénytelenek a megadott" egy érvénytelen tároló hitelesítőadat-hiba, a tároló hitelesítési adatait tartalmazó fájlt a sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó. Próbálja megismételni a műveletet, miután egy új tárolói hitelesítő adatok fájlját letöltése a portálról. Ezt a hibát általában az látható, amikor a felhasználó rákattint a **letöltési tárolóhitelesítő adatokat** az Azure portálon, gyors egymásutánban lehetőséget. Ez akkor fordul elő, amikor csak a második tárolói hitelesítő adatok fájlját érvénytelen.
9. Az a **titkosítási beállítás** képernyő, megadhat egy hozzáférési kódot létrehozni, vagy adjon meg egy jelszót (minimum 16 karakter). Ne felejtse el a hozzáférési kód mentse egy biztonságos helyre.
   
    ![Titkosítás](./media/backup-install-agent/encryption.png)
   
   > [!WARNING]
   > Ha a jelszó elvesztése vagy elfelejtése; A Microsoft nem megkönnyíti a biztonsági mentési adatok helyreállítása. A felhasználó tulajdonában van a titkosítás jelszava és a Microsoft nem látnak bele a végfelhasználó által használt jelszót. Mentse a fájlt egy biztonságos helyre, szükség van egy helyreállítási művelet során.
   > 
   > 
10. Miután rákattintott a **Befejezés** gombra, a gép sikeresen regisztrálva van a tárolóba, és most már készen indítsa el a biztonsági mentés a Microsoft Azure.
11. A Microsoft Azure Backup szolgáltatás önálló használata esetén a során megadott beállítások a regisztrációs munkafolyamat kattintva módosíthatja a **tulajdonságainak módosítása** beállítása az Azure Backup szolgáltatás MMC beépülő modul.
    
    ![Tulajdonságainak módosítása](./media/backup-install-agent/change.png)
    
    Azt is megteheti, a Data Protection Manager használata esetén is a beállítások módosítása gombra kattintva a regisztrációs munkamenet során megadott a **konfigurálása** lehetőség kiválasztásával **Online** alatt a **Felügyeleti** fülre.
    
    ![Az Azure Backup konfigurálása](./media/backup-install-agent/configure.png)

