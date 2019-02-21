---
title: Az Azure Backup Agent hibaelhárítása
description: Telepítési és regisztrációs Azure Backup Agent hibaelhárítása
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: ce6293e63e672df9683ab607a304f8c7275911c5
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446613"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services-(MARS-) ügynök hibaelhárítása

Oldja meg az esetlegesen előforduló hibák során konfigurációja, a regisztráció, a biztonsági mentés, a következőképpen és visszaállításához.

## <a name="invalid-vault-credentials-provided"></a>Megadott tárhitelesítő adatok érvénytelenek
| A hiba részletei | A lehetséges okok | Javasolt műveletek |
| ---     | ---     | ---    |
| **Hiba történt** </br> *A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatok a helyreállítási szolgáltatáshoz hozzárendelt. (AZONOSÍTÓ: 34513)* | <ul><li> A tároló hitelesítő adatai érvénytelenek. (azt jelenti, azok letöltése megtörtént a regisztráció előtt legfeljebb 48 óra).<li>A MARS-ügynök nem tudja fájlok letöltése a Windows Temp könyvtárában. <li>A tároló hitelesítő adatai vannak egy hálózati helyre. <li>A TLS 1.0 le van tiltva.<li> Egy konfigurált proxykiszolgálón blokkolja a kapcsolatot. <br> |  <ul><li>Töltse le a tár új hitelesítő adatait. (**Megjegyzés**: Ha több tároló hitelesítő adatfájljait a korábban letöltött, csak a legújabb letöltött fájl nem érvényes 48 órán belül.) <li>Indítsa el a **IE** > **beállítás** > **Internetbeállítások** > **biztonsági**  >  **Internet**. Majd **Egyéni szint**, amíg meg nem látja a fájl letöltése szakasz görgessen. Válassza ki **engedélyezése**.<li>Akkor is lehet ezeken a webhelyeken hozzáadása az Internet Explorer [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait. <li> A dátum és idő egyezik a gépen.<li>Ha hibaüzenet jelenik meg, hogy a fájlok letöltése nem engedélyezettek, valószínű, hogy nincsenek-e egy nagy mennyiségű fájlt a C:/Windows/Temp könyvtárba.<li>C:/Windows/Temp nyissa meg, és ellenőrizze, hogy vannak-e több mint 60 000 vagy 65,000 .tmp kiterjesztésű fájlt. Ha vannak, ezeket a fájlokat törli.<li>Győződjön meg arról, hogy a .NET-keretrendszer 4.6.2-es. <li>Ha PCI-megfelelőség miatt le van tiltva a TLS 1.0, tekintse meg a [hibaelhárítási lap](https://support.microsoft.com/help/4022913). <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskeresés: <ul><li>CBengine.exe<li>CSC.exe, amely kapcsolódik a .NET-keretrendszer. A CSC.exe minden .NET-verzió, amely a kiszolgálón van telepítve van. Az érintett kiszolgálón .NET-keretrendszer összes verziójához kötött CSC.exe fájlok kizárása. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>A bin mappa: C:\Program Files\Microsoft Azure Recovery Services Agent\Bin

## <a name="unable-to-download-vault-credential-file"></a>Nem sikerült letölteni a tároló hitelesítőadat-fájlja

| A hiba részletei | Javasolt műveletek |
| ---     | ---    |
|Nem sikerült letölteni a tár hitelesítő adatait tartalmazó fájlt. (AZONOSÍTÓ: 403) | <ul><li> Próbálja meg letölteni a tároló hitelesítő adatait a másik böngészővel, vagy hajtsa végre az alábbi lépéseket: <ul><li> F12 billentyű IE, indítsa el. </li><li> Lépjen a **hálózati** fülre, és törölje az Internet Explorer cache és a cookie-k </li> <li> Frissítse a lapot<br>(OR)</li></ul> <li> Ellenőrizze, hogy az előfizetés le van tiltva vagy lejárt<br>(OR)</li> <li> Ellenőrizze, hogy ha bármilyen tűzfalszabály blokkolja a tároló hitelesítőadat-fájl letöltése <br>(OR)</li> <li> Győződjön meg arról, nem merül ki a korlátot, a tároló (tárolónként 50 gépet)<br>(OR)</li>  <li> Győződjön meg arról, felhasználói töltse le a tároló hitelesítő adatait, és regisztrálja a kiszolgálót a tárolóban, olvassa el az Azure Backup engedély szükséges [cikk](backup-rbac-rs-vault.md)</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>A Microsoft Azure Recovery Service-ügynök nem tudott kapcsolódni a Microsoft Azure Backuphoz

| A hiba részletei | A lehetséges okok | Javasolt műveletek |
| ---     | ---     | ---    |
| **Hiba történt** </br><ol><li>*A Microsoft Azure Recovery Service Agent nem tudott kapcsolatot a Microsoft Azure Backup szolgáltatásban. (AZONOSÍTÓ: 100050) Ellenőrizze a hálózati beállításokat, és győződjön meg arról, hogy tud csatlakozni az internethez*<li>*(407) Proxyhitelesítés szükséges* |A proxy blokkolja a kapcsolatot. |  <ul><li>Indítsa el a **IE** > **beállítás** > **Internetbeállítások** > **biztonsági**  >  **Internet**. Válassza ki **Egyéni szint** görgessen, amíg megjelenik a fájl letöltése szakaszban. Válassza az **Engedélyezés** lehetőséget.<li>Akkor is lehet ezeken a webhelyeken hozzáadása az Internet Explorer [megbízható helyek](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Módosítsa a beállításokat, egy proxykiszolgáló használatára. Adja meg a proxy adatait. <li>Ha a kiszolgálón telepített víruskereső szoftver, a következő fájlok kizárása a víruskeresés. <ul><li>CBEngine.exe (helyett dpmra.exe).<li>A CSC.exe (.NET-keretrendszer kapcsolatos). A CSC.exe minden .NET-verzió, amely a kiszolgálón van telepítve van. Az érintett kiszolgálón .NET-keretrendszer összes verziójához kötött CSC.exe fájlok kizárása. <li>Ideiglenes mappa vagy a gyorsítótár helyét. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>A bin mappa: C:\Program Files\Microsoft Azure Recovery Services Agent\Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni

| A hiba részletei | A lehetséges okok | Javasolt műveletek |
| ---     | ---     | ---    |      
| **Hiba történt** </br>*Nem sikerült beállítani a titkosítási kulcs biztonsági mentést készíteni. Az aktiválás nem sikerült teljesen, de a titkosítási jelszó mentése a következő fájl*. |<li>A kiszolgáló már regisztrálva van egy másik tárolóval.<li>A jelszó konfigurálása során sérült volt.| Törölje a tárolót a kiszolgáló regisztrációját, és regisztrálja újra az új jelszóval.

## <a name="the-activation-did-not-complete-successfully"></a>Az aktiválás nem sikerült

| A hiba részletei | A lehetséges okok | Javasolt műveletek |
|---------|---------|---------|
|**Hiba történt** </br><ol>*Az aktiválás sikertelenül zárult. Az aktuális művelet belső szolgáltatáshiba [0x1FC07] miatt nem sikerült. Próbálja meg újra a műveletet később. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához*     | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.         | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>A biztonsági mentési adatok teljes mérete 5 – 10 %-ával egyenlő szabad lemezterülettel rendelkező kötetre helyezze át az ideiglenes mappát vagy a gyorsítótár helyét. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Titkosítási jelszó nincs megfelelően konfigurálva

| A hiba részletei | A lehetséges okok | Javasolt műveletek |
|---------|---------|---------|
|**Hiba történt** </br><ol>*Hiba történt a 34506. Ezen a számítógépen tárolt titkosítási jelszava helytelenül van konfigurálva*.    | <li> Az ideiglenes mappát, amely nincs elegendő lemezterület a köteten található. <li> Az ideiglenes mappát helytelenül áthelyezték egy másik helyre. <li> A OnlineBackup.KEK fájl hiányzik.        | <li>Frissítés a [legújabb verzió](https://aka.ms/azurebackup_agent) a MARS-ügynök.<li>Helyezze át az ideiglenes mappát vagy a gyorsítótár helyét egy biztonsági mentési adatok teljes mérete 5 – 10 %-ának megfelelő szabad lemezterülettel rendelkező kötetre. A gyorsítótár helyének megfelelően áthelyezéséhez tekintse meg a lépéseket a [az Azure Backup ügynök kapcsolatos kérdéseket](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Győződjön meg arról, hogy a OnlineBackup.KEK fájl jelen-e. <br>*Az ideiglenes mappát vagy a gyorsítótár elérési útjához alapértelmezett helye a C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Biztonsági másolatok ne futtassa a megadott ütemezés szerint
Ha ütemezett biztonsági mentések nem lekérése automatikusan, amíg a manuális biztonsági mentések problémamentesen működik, próbálja meg a következő műveleteket:

- Győződjön meg arról, a Windows Server biztonsági mentés ütemezése nem ütközik az Azure-fájlok és mappák biztonsági mentés ütemezése.
- Lépjen a **vezérlőpultot** > **felügyeleti eszközök** > **Feladatütemező**. Bontsa ki a **Microsoft**, és válassza ki **Online biztonsági mentés**. Kattintson duplán a **Microsoft-OnlineBackup**, és nyissa meg a **eseményindítók** fülre. Győződjön meg arról, hogy az állapot értéke **engedélyezve**. Ha nem, válassza ki a **szerkesztése**, és válassza ki a **engedélyezve** jelölőnégyzetet, majd kattintson **OK**. Az a **általános** lépjen **biztonsági beállítások** , és győződjön meg arról, hogy a feladat futtatásához a kiválasztott felhasználói fiók vagy **rendszer** vagy **helyi Rendszergazdák csoport** a kiszolgálón.

- Tekintse meg, ha a PowerShell 3.0-s vagy újabb verziója telepítve van-e a kiszolgálón. A PowerShell-verziójának ellenőrzéséhez futtassa a következő parancsot, és ellenőrizze, hogy a *fő* verziószáma 3-nál nagyobb vagy egyenlő.

  `$PSVersionTable.PSVersion`

- A következő elérési út része-e a *PSMODULEPATH* környezeti változót.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Ha a PowerShell végrehajtási szabályzata a *LocalMachine* van beállítva a korlátozott, a PowerShell-parancsmag, amely elindítja a biztonsági mentési feladat sikertelen lehet. Futtassa az alábbi parancsokat emelt jogosultságszintű módban, és a végrehajtási házirend beállítására vagy *Unrestricted* vagy *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Győződjön meg arról, hogy módosítások következetesek legyenek, hogy a fenti lépések végrehajtása után indítsa újra a kiszolgálót.


## <a name="troubleshoot-restore-issues"></a>Visszaállítási hibáinak elhárítása

Az Azure Backup előfordulhat, hogy nem sikerült csatlakoztatni a helyreállítási kötetet, akár néhány perc múlva. Hibaüzenetek is kaphat a folyamat során. Általában a helyreállítás megkezdéséhez kövesse az alábbi lépéseket:

1.  Megszakítja a folyamatban lévő csatlakozási folyamat, abban az esetben több percig futott.

2.  Tekintse meg, hogy a Backup szolgáltatás ügynökének legújabb verziója. Tudja meg a verziót a a **műveletek** a MARS-konzolon válassza a panel **kapcsolatban a Microsoft Azure Recovery Services Agent**. Ellenőrizze, hogy a **verzió** szám egyenlő vagy nagyobb, mint az említett verzió [Ez a cikk](https://go.microsoft.com/fwlink/?linkid=229525). Letöltheti a legújabb verziót [Itt](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Lépjen a **Eszközkezelő** > **tárolóvezérlők**, és keresse meg **Microsoft iSCSI-kezdeményező**. Ha meg tudja találni, folytassa a 7.

4.  Ha nem találja a Microsoft iSCSI-kezdeményező szolgáltatást, próbálja meg, bejegyzés alatt található **Eszközkezelő** > **tárolóvezérlők** nevű **ismeretlen eszköz**, a Hardverazonosítót **ROOT\ISCSIPRT**.

5.  Kattintson a jobb gombbal a **ismeretlen eszköz**, és válassza ki **illesztőprogram frissítése**.

6.  A beállítás kiválasztásával az illesztőprogram frissítésével **keressen automatikusan a frissített illesztőprogram**. A frissítés befejezése után meg kell változtatni **ismeretlen eszköz** való **Microsoft iSCSI-kezdeményező**lent látható módon.

    ![Képernyőkép az Azure biztonsági mentés az Eszközkezelő, a kiemelt tárolóvezérlők](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Lépjen a **Feladatkezelő** > **szolgáltatások (helyi)** > **Microsoft iSCSI-kezdeményező szolgáltatás**.

    ![Képernyőkép az Azure biztonsági mentési feladat-kezelő szolgáltatások (helyi) kiemeli a](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Indítsa újra a Microsoft iSCSI-kezdeményező szolgáltatás. Ehhez kattintson a jobb gombbal a szolgáltatáshoz, válassza a **leállítása**, a jobb gombbal ismét, és válassza ki **Start**.

9.  Próbálja meg újra a helyreállítást a [ **azonnali visszaállítása**](backup-instant-restore-capability.md).

Ha a helyreállítás is sikertelen, indítsa újra a kiszolgáló vagy ügyfél. Ha nem szeretne újraindítani, vagy a helyreállítás továbbra is a kiszolgáló újraindítása után is sikertelen, próbálkozzon egy másik számítógépre történő helyreállítás. Kövesse a [Ez a cikk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

## <a name="next-steps"></a>További lépések
* Részletes adatainak [hogyan készíthet biztonsági másolatot a Windows Server az Azure Backup-ügynökkel](tutorial-backup-windows-server-to-azure.md).
* Ha vissza kell állítania egy biztonsági másolatot, ezzel a cikkel [állíthat vissza fájlokat Windows rendszerű gépre](backup-azure-restore-windows-server.md).
