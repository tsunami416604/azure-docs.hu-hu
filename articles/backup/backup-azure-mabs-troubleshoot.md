---
title: Az Azure Backup Server hibaelhárítása
description: Végezzen hibaelhárítást a regisztráció az Azure Backup Server és a biztonsági mentés és visszaállítás az alkalmazások és szolgáltatások telepítése.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: kasinh
ms.openlocfilehash: 22507a1b89c6a7d6867e9b669e1a2e70106a4e41
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880568"
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

Az alábbi táblázatokban szereplő információk segítségével, amely az Azure Backup Server használata során előforduló hibák elhárítása.

## <a name="invalid-vault-credentials-provided"></a>Megadott tárhitelesítő adatok érvénytelenek

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Tárolóra regisztrálása | A tároló megadott hitelesítő adatai érvénytelenek. A fájl sérült, vagy nem nem rendelkezik a hozzárendelt legfrissebb hitelesítő adatokkal a szolgáltatásban. | Javasolt művelet: <br> <ul><li> Töltse le a legújabb hitelesítő adatok fájlt a tárolóból, és próbálkozzon újra. <br>(OR)</li> <li> Ha az előző művelet nem működik, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót. <br>(OR)</li> <li> Próbálja meg frissíteni a dátum- és időbeállítások, leírtak szerint [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Ellenőrizze, hogy rendelkezik-e c:\windows\temp több mint 65000 fájlok. Elavult fájlok áthelyezése egy másik helyre, vagy törli a elemeket a Temp mappában. <br>(OR)</li> <li> A tanúsítvány állapotának ellenőrzéséhez. <br> a. Nyissa meg **számítógép-tanúsítványok kezelése** (a Vezérlőpulton). <br> b. Bontsa ki a **személyes** csomópontot és annak gyermek csomópont **tanúsítványok**.<br> c.  Távolítsa el a tanúsítványt **Windows Azure-eszközökkel**. <br> d. Ismételje meg a regisztráció az Azure Backup-ügyfél. <br> (OR) </li> <li> Ellenőrizze, hogy ha a csoportházirend van beállítva. </li></ul> |

## <a name="replica-is-inconsistent"></a>Inkonzisztens replika

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Inkonzisztens replika | Győződjön meg arról, hogy be van kapcsolva az automatikus konzisztencia ellenőrzése a beállítás a védelmi csoport varázslóban. A replika inkonzisztenciája és a kapcsolódó javaslatokat az okok kapcsolatos további információkért tekintse meg a Microsoft TechNet következő cikkében [inkonzisztens replika esetén](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Esetén a rendszerállapot vagy BMR biztonsági mentéssel győződjön meg arról, hogy a Windows Server biztonsági másolat telepítve van a védett kiszolgálón.</li><li> Keresse meg a DPM vagy a Microsoft Azure Backup Server a DPM-tárolókészletben helyet szolgáltatással kapcsolatos problémák, és szükség szerint tárterület foglalása.</li><li> Ellenőrizze a kötet árnyékmásolata szolgáltatás a védett kiszolgáló állapotát. Ha egy letiltott állapotban van, állítsa be, hogy indítsa el manuálisan. Indítsa el a szolgáltatást a kiszolgálón. Ezután lépjen vissza a DPM vagy a Microsoft Azure Backup Server konzolt, és indítsa el a szinkronizálás és a konzisztencia-ellenőrzést.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Az online helyreállítási pont létrehozása sikertelen volt.

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Az online helyreállítási pont létrehozása sikertelen volt. | **Chybová Zpráva**: Windows Azure Backup szolgáltatás ügynökének nem sikerült pillanatfelvételt készíteni a kijelölt kötetről. <br> **Megkerülő megoldás**: Próbálja növelni a területet az replikát és helyreállításipont-kötetre.<br> <br> **Chybová Zpráva**: A Windows Azure Backup szolgáltatás ügynöke nem tud kapcsolódni az OBEngine szolgáltatáshoz <br> **Megkerülő megoldás**: Győződjön meg arról, hogy létezik-e az OBEngine a számítógépen futó szolgáltatások listájában. Az OBEngine szolgáltatás nem fut, ha a "net start OBEngine" parancs segítségével indítsa el az OBEngine szolgáltatáshoz. <br> <br> **Chybová Zpráva**: Nincs beállítva ez a kiszolgáló titkosítási jelszava. Állítsa be a szükséges jelszót. <br> **Megkerülő megoldás**: Próbálja ki egy titkosítási jelszó konfigurálása. Ha nem sikerül, a következő lépéseket: <br> <ol><li>Győződjön meg arról, hogy létezik-e az ideiglenes hely. Ez az a hely, amely szerepel a beállításjegyzék **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, nevű **ScratchLocation** léteznie kell.</li><li> Ha az ideiglenes hely már létezik, próbálja meg újra regisztrálja a régi jelszó használatával. *Minden alkalommal, amikor egy titkosítási jelszót konfigurál, mentse azt egy biztonságos helyre.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>A megadott tárhitelesítő adatok nem azonosak a tárolóban, a kiszolgáló regisztrálva van

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Visszaállítás | **Hibakód:**: CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Chybová zpráva**: A megadott tárhitelesítő adatok nem azonosak a tárolóban, a kiszolgáló regisztrálva van | **Ok**: Ez a probléma akkor fordul elő, ha visszaállítja a fájlokat a másodlagos kiszolgálóra az eredeti kiszolgáló külső DPM-helyreállítási lehetőséget a kívánt, és ha a kiszolgáló, amely a helyreállítás alatt áll, és az eredeti kiszolgálón, ahol az adatok-e készíteni a rendszer nem társítja azonos Recovery Services-tároló.<br/> <br/>**Megkerülő megoldás** feloldani a probléma győződjön meg arról, mind az eredeti és a másodlagos kiszolgáló ugyanahhoz a tárolóhoz van regisztrálva.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware virtuális gép online helyreállításipont-létrehozási feladatok sikertelen

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | VMware virtuális gép online helyreállításipont-létrehozási feladatok sikertelenek. A DPM a VMware-ből hibát észlelt a változáskövetési információk beolvasása közben. ErrorCode - FileFaultFault (ID 33621 ) |  <ol><li> A VMware-en CTK alaphelyzetbe az érintett virtuális gépek számára.</li> <li>Ellenőrizze, hogy a független lemez nem VMware-en vannak érvényben.</li> <li>Állítsa le az érintett virtuális gépek védelmét, és az ismételt védelem a **frissítése** gombra. </li><li>Az érintett virtuális gépek futtatása egy másolatot kap.</li></ol>|


## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Az ügynök művelete sikertelen volt a DPM az ügynök koordinátor szolgáltatást a kiszolgálón kommunikációs hiba miatt

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök leküldése a védett kiszolgálók | Az ügynök művelete sikertelen volt a DPM-Ügynökkoordinátor szolgáltatással kommunikációs hiba miatt a \<kiszolgáló_neve >. | **Ha a javasolt művelet látható a termék nem működik, hajtsa végre az alábbi lépéseket**: <ul><li> Ha egy számítógép nem megbízható tartományból való csatlakoztatás, hajtsa végre a [ezeket a lépéseket](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> Ha egy számítógép megbízható tartományban való csatlakoztatás, hibáinak elhárítása az ismertetett lépéseket követve [ebben a blogbejegyzésben](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Próbálja ki a letiltás víruskereső hibaelhárítás céljából. Ha feloldja a problémát, javasolt a víruskereső beállításainak módosításához [Ez a cikk](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat

| Művelet | A hiba részletei | Áthidaló megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat. A frissítési hiba a tárhelyhasználat overusage vezethet. Ennek elkerülése érdekében frissítse az ReFS-csonkolás beállításjegyzék-bejegyzést. | A beállításkulcs beállítása **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Állítsa a Dword értékét 1-re. |
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzékbeli metaadatokat. A frissítési hiba a tárhelyhasználat overusage vezethet. Ennek elkerülése érdekében frissítse a kötet SnapOptimization beállításjegyzék-bejegyzést. | A beállításkulcs létrehozása **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** egy üres karakterláncot tartalmazó. |

## <a name="registration-and-agent-related-issues"></a>Regisztráció és az ügynökkel kapcsolatos problémák

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Ügynök leküldése a védett kiszolgálók | A kiszolgáló megadott hitelesítő adatok érvénytelenek. | **Ha a javasolt művelet található a termék nem működik, a következő lépésekkel**: <br> Próbálja ki a védelmi ügynök manuális telepítése az üzemi kiszolgálón megadott [Ez a cikk](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Az Azure Backup szolgáltatás ügynökének nem sikerült kapcsolódni az Azure Backup szolgáltatás (azonosító: 100050) | Az Azure Backup szolgáltatás ügynökének nem sikerült kapcsolódni az Azure Backup szolgáltatással. | **Ha a javasolt művelet található a termék nem működik, a következő lépésekkel**: <br>1. Futtassa a következő parancsot egy rendszergazda jogú parancssorból: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Ekkor megnyílik az Internet Explorer-ablakban. <br/> 2. Lépjen a **eszközök** > **Internetbeállítások** > **kapcsolatok** > **LAN-beállítások**. <br/> 3. Ellenőrizze a proxykiszolgáló beállításait a system fiók. Állítsa be a proxykiszolgáló IP-címet és portot. <br/> 4. Zárja be az Internet Explorerben.|
| Az Azure Backup szolgáltatás ügynökének telepítése nem sikerült | Nem sikerült telepíteni a Microsoft Azure Recovery Services. A rendszer a Microsoft Azure Recovery Services-berendezés által végrehajtott összes módosítás vissza lett állítva. (AZONOSÍTÓ: 4024) | Az Azure-ügynök manuális telepítése.


## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudta felsorolni az alkalmazás-összetevő, az a védett számítógép (védett számítógép neve). | Válassza ki **frissítése** a konfigurálás védelmi csoport felhasználói felület képernyőn a megfelelő adatforrás/összetevő szintjén. |
| Védelmi csoportok konfigurálása | Nem sikerült beállítani a védelmet. | Ha egy SQL-kiszolgáló a védett kiszolgálón, győződjön meg arról, hogy a sysadmin (rendszergazda) szerepkör engedélyeket adtak meg a védett számítógépen a system fióknak (NTAuthority\System) leírtak szerint [Ez a cikk](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Védelmi csoportok konfigurálása | Nincs elegendő szabad terület a védelmi csoport számára a tárolókészletben. | A lemezeket a tárolókészlethez hozzáadott [nem tartalmazhat egy partíció](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Törölje a meglévő köteteket a lemezeken. Adja hozzá a tárolókészlethez.|
| Szabályzat módosítása |Nem sikerült módosítani a biztonsági mentési szabályzat. Hiba: Az aktuális művelet belső szolgáltatáshiba [0x29834] miatt nem sikerült. Némi várakozás után próbálkozzon újra a művelettel. Ha a probléma tartósan fennáll, forduljon a Microsoft ügyfélszolgálatához. | **OK:**<br/>Ez a hiba akkor fordul elő, a három feltétel: amikor biztonsági beállításai engedélyezve vannak, csökkenteni a megőrzési időtartam alatt a korábban megadott minimális értékek meg, és amikor a rendszer nem támogatott verzióját. (Nem támogatott verziók által alábbi 2.0.9052-es verzióját a Microsoft Azure Backup Server és az Azure Backup Server frissítése 1.) <br/>**Javasolt művelet:**<br/> -Házirendekkel kapcsolatos frissítések folytatásához, fent a minimális megőrzési időszak a megadott megőrzési idejének beállítása. (A minimális megőrzési időszak a hét nap a napi, heti, három hét szükséges 4 hetes havonta vagy egy év az éves.) <br><br>Ha szükséges, egy másik az előnyben részesített módszer az, hogy frissítse a backup-ügynök és az Azure Backup Server kihasználhatja a biztonsági frissítéseket. |

## <a name="backup"></a>Backup

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Backup | Váratlan hiba történt a feladat futása közben. Az eszköz nem áll készen. | **Ha a javasolt művelet található a termék nem működik, tegye a következőket:** <br> <ul><li>Állítsa be az árnyékmásolat-tároló terület a védelmi csoport elemeken korlátlanra, és futtassa a konzisztencia-ellenőrzést.<br></li> (OR) <li>Próbálja meg törölni a meglévő védelmi csoportot, és több új csoport létrehozása. Minden egyes új védelmi csoport egyetlen elemet kell benne.</li></ul> |
| Backup | Ha csak a rendszerállapotot biztonsági, győződjön meg arról, hogy van-e elég szabad hely a védett számítógépen a rendszerállapot biztonsági másolatának tárolásához. | <ol><li>Győződjön meg arról, hogy a Windows Server biztonsági másolat a védett gépen telepítve van.</li><li>Győződjön meg arról, hogy van-e elég hely a védett számítógépen a rendszerállapot. A legegyszerűbben úgy, hogy e, nyissa meg a védett számítógépen nyissa meg a Windows Server biztonsági másolat, a kijelölések kattintással, és kattintson az operációs rendszer nélküli Helyreállítás. A felhasználói felület majd kiderül, hogy mekkora terület megadása kötelező. Nyissa meg **WSB** > **helyi biztonsági másolat** > **biztonsági mentés ütemezése** > **válassza ki a biztonsági mentés konfigurációja**  >  **Teljes kiszolgáló** (méret jelenik meg). Ez a méret használni az ellenőrzéshez.</li></ol>
| Backup | Sikertelen volt a BMR biztonsági mentés | Ha az operációs rendszer nélküli Helyreállítás mérete nagy, át néhány alkalmazás az operációs rendszer meghajtójának, és próbálkozzon újra. |
| Backup | Egy új Microsoft Azure Backup Server a VMware virtuális gép védelmének újbóli beállításához a beállítás nem jelenik meg szerint adhatók hozzá. | VMware-tulajdonságok hivatkozott vannak a Microsoft Azure Backup Server régi, elavult példányát. A probléma megoldása:<br><ol><li>A VCenter (SC-VMM egyenértékű), nyissa meg a **összefoglalás** fülre, majd a **Vlastní Atributy**.</li>  <li>Törölje a régi, a Microsoft Azure Backup Server nevét a **DPMServer** értéket.</li>  <li>Lépjen vissza az új Microsoft Azure Backup Server, és módosítsa a old.  Miután kiválasztotta a **frissítése** gombra, a virtuális gép megjelenik egy jelölőnégyzet bejelölésével elérhető telepítésként, hozzáadja a védelemhez.</li></ol> |
| Backup | Hiba történt a megosztott fájlok/mappák elérése | Próbálja meg módosítani a víruskereső beállításainak a TechNet-cikkben leírtak [víruskereső szoftver futtatása a DPM-kiszolgálón](https://technet.microsoft.com/library/hh757911.aspx).|


## <a name="change-passphrase"></a>Jelszó módosítása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Jelszó módosítása |A biztonsági megadott PIN-kód helytelen. Adja meg a helyes biztonsági PIN-kód, a művelet végrehajtásához. |**OK:**<br/> Ez a hiba akkor fordul elő, ha beír egy érvénytelen, vagy a biztonsági PIN-kód lejárt, egy kritikus fontosságú művelet (például a jelszó módosítása) hajt végre. <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához meg kell adnia egy érvényes biztonsági PIN-kód. A PIN-kód jelentkezzen be az Azure Portalon, és nyissa meg a Recovery Services-tároló. Ezután lépjen a **beállítások** > **tulajdonságok** > **biztonsági PIN-kód készítése**. A jelszó módosításához használja a PIN-kódot. |
| Jelszó módosítása |Sikertelen volt a művelet. Azonosító: 120002 |**OK:**<br/>Ez a hiba akkor fordul elő, amikor biztonsági beállításai engedélyezve vannak, vagy amikor megpróbálja módosítani a jelszót, ha egy nem támogatott verzióját használja.<br/>**Javasolt művelet:**<br/> A jelszó módosításához először frissítenie kell a backup-ügynök a minimális verzióra, amely 2.0.9052. Ön emellett minimális értéke 1. frissítés az Azure Backup Server frissítése, és adja meg egy érvényes biztonsági PIN-kód. A PIN-kód lekéréséhez jelentkezzen be az Azure Portalra, és lépjen a Recovery Services-tároló. Ezután lépjen a **beállítások** > **tulajdonságok** > **biztonsági PIN-kód készítése**. A jelszó módosításához használja a PIN-kódot. |


## <a name="configure-email-notifications"></a>E-mail értesítések konfigurálása

| Művelet | A hiba részletei | Áthidaló megoldás |
| --- | --- | --- |
| Használja az Office 365-fiókja e-mail-értesítések beállítása |Hiba azonosítója: 2013| **OK:**<br> Próbálja meg használni az Office 365-fiókkal <br>**Javasolt művelet:**<ol><li> A rendszer az első lépésben, győződjön meg arról, hogy a "Engedélyezése névtelen Relay a egy fogadási összekötőn" a DPM-kiszolgáló az Exchange van beállítva. Konfigurálásával kapcsolatos további információkért lásd: [található egy fogadási összekötőn névtelen továbbító engedélyezése](https://technet.microsoft.com/library/bb232021.aspx) a TechNet webhelyén.</li> <li> Ha nem használja a belső SMTP-továbbítás és be kell állítania az Office 365-kiszolgáló használatával, akkor állíthat be az IIS lennie a továbbító. A DPM-kiszolgáló konfigurálása [továbbítja az IIS-sel az O365 az SMTP](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx).<br><br> **FONTOS:** Ügyeljen arra, hogy a felhasználó\@tartomány.com formátum és *nem* tartomány\felhasználó.<br><br><li>Használja a helyi kiszolgáló nevét, SMTP-kiszolgáló pont a DPM az 587-es port. Ezután mutasson, a felhasználó e-mail-címe, amely az e-maileket kell származnia.<li> A felhasználónevet és jelszót a DPM SMTP telepítés oldalon egy olyan tartományi fiók, amely a DPM a tartományban kell lennie. </li><br> **MEGJEGYZÉS**: Ha módosítja a SMTP-kiszolgáló címét, végezze el a módosítást az új beállítások, a beállítások panel bezárásához, és majd újból megnyitja, és ellenőrizze, hogy tükrözze az új értéket.  Egyszerűen módosítása és tesztelése előfordulhat, hogy nem mindig okozhat az új beállítások érvényesítéséhez, így a legjobb tesztelésére is így.<br><br>Ez a folyamat során bármikor törölheti ezeket a beállításokat a DPM-konzol bezárása, és a következő beállításkulcsok szerkesztésével: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> SMTPPassword törlése és SMTPUserName kulcsok**. Felveheti őket vissza a felhasználói felület, amikor újra elindítani.
