---
title: "Az Azure Backup Server hibaelhárítása |} Microsoft Docs"
description: "A telepítés, a regisztráció az Azure Backup Server és a biztonsági mentés és visszaállítás az alkalmazások és szolgáltatások hibaelhárítása"
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: 7ef808e933d1c3ff88e18766cd3a29138959297a
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Az Azure Backup Server hibaelhárítása

Észlelt, miközben az Azure Backup Server használatával információkat a következő táblázatban felsorolt hibák is elháríthatók.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Hiba: Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó 

Kövesse az alábbi [hibaelhárítási] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) a probléma megoldásához.

## <a name="error-the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-server"></a>Hiba: Az ügynök működése leállt, a DPM-Ügynökkoordinátor szolgáltatást a kiszolgálón kommunikációs hiba miatt 

Kövesse az alábbi [hibaelhárítási] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) a probléma megoldásához.

## <a name="error-setup-could-not-update-registry-metadata"></a>Hiba: A telepítés nem sikerült frissíteni a beállításjegyzék-metaadatok

Kövesse az alábbi [hibaelhárítási] (https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) a probléma megoldásához.


## <a name="installation-issues"></a>Telepítési problémák

| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
|-----------|---------------|------------|
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzék-metaadatok. A frissítés hiba előfordulhat, hogy keresztül tárhelyhasználatot használatát. Elkerülése érdekében ez frissítse a refs fájlrendszer díszítésre beállításjegyzék-bejegyzés. | Módosíthatja a beállításkulcsot, SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Állítsa a Dword értékét 1. |
|Telepítés | A telepítő nem tudta frissíteni a beállításjegyzék-metaadatok. A frissítés hiba előfordulhat, hogy keresztül tárhelyhasználatot használatát. Elkerülése érdekében ez frissítse a mennyiségi SnapOptimization beállításjegyzék-bejegyzés. | A beállításkulcs, SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds, hozzon létre üres karakterlánc. |

## <a name="registration-and-agent-related-issues"></a>Regisztráció és az ügynök kapcsolatos problémák
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| A tárolóba való regisztrálása | Érvénytelen megadott tárolói hitelesítő adatok. A fájl sérült, vagy nem nem rendelkezik a legújabb hitelesítő adatokat a helyreállítási szolgáltatáshoz tartozó | Ez a hiba elhárításához: <ol><li> Töltse le a legfrissebb hitelesítőadat-fájlt a tárolóból, és próbálkozzon újra <br>(VAGY)</li> <li> Ha a fenti művelet nem működik, próbálja meg letölteni a hitelesítő adatokat egy másik helyi könyvtárba, vagy hozzon létre egy új tárolót <br>(VAGY)</li> <li> Próbálja meg frissíteni a dátum és idő beállítása leírtaknak [ebben a blogban](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/) <br>(VAGY)</li> <li> Ellenőrizze, hogy c:\windows\temp több mint 65000 fájlokat tartalmaz. Elavult fájlok áthelyezése egy másik helyre, vagy az ideiglenes mappában az elemek törlése <br>(VAGY)</li> <li> A tanúsítványok állapotának ellenőrzése <br> a. Nyissa meg a "Számítógép-tanúsítványok kezelése" (a Vezérlőpulton) <br> b. Bontsa ki a "Privát" csomópont és az alárendelt csomópont "Tanúsítványok" <br> c.  Távolítsa el a tanúsítványt "Windows Azure eszközök" <br> d. Ismételje meg a regisztráció az Azure Backup-ügyfél <br> (VAGY) </li> <li> Ellenőrizze, hogy a csoportházirend érvényben </li></ol> |
| Az ügynököket a védett kiszolgálók küldését | Az ügynök művelete sikertelen volt a DPM-Ügynökkoordinátor szolgáltatással kommunikációs hiba miatt \<kiszolgálónév > | **Ha a termék látható javasolt művelet nem működik**, <ol><li> Ha egy számítógép nem megbízható tartományból konfigurálhatóak, kövesse az [ezek](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) lépéseket <br> (VAGY) </li><li> Ha a számítógép megbízható tartományhoz csatlakoztatás, hibáinak elhárítása az itt leírt lépésekkel [ebben a blogban](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/) <br>(VAGY)</li><li> Próbálja meg a víruskereső hibaelhárítás céljából letiltása. Ha ez megoldja a problémát, várja meg a víruskereső leírtak [ebben a cikkben] (https://technet.microsoft.com/library/hh757911.aspx)</li></ol> |
| Az ügynököket a védett kiszolgálók küldését | A kiszolgálóhoz megadott hitelesítő adatok érvénytelenek. | **Ha a termék látható javasolt művelet nem működik**, <br> a védelmi ügynök manuális telepítése az üzemi kiszolgálón megadott próbálja [Ez a cikk](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)|
| Az Azure Backup szolgáltatás ügynöke nem tudott kapcsolódni az Azure Backup szolgáltatáshoz (azonosító: 100050) | Az Azure Backup szolgáltatás ügynökének nem sikerült csatlakozni az Azure Backup szolgáltatás volt. | **Ha a termék látható javasolt művelet nem működik**, <br>1. Futtassa a következő parancs rendszergazda jogú parancssorból, a psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe" az internet explorer-ablakban nyílik. <br/> 2. Ugrás a eszközök -> Internet-beállítások -> kapcsolatok LAN-beállítások ->. <br/> 3. Ellenőrizze a proxybeállítások helyességét a rendszerfiók. Állítsa be a Proxy IP-cím és port. <br/> 4. Zárja be az Internet Explorert.|
| Az Azure Backup szolgáltatás ügynökének telepítése nem sikerült | A Microsoft Azure Recovery Services telepítése nem sikerült. A Microsoft Azure Recovery Services telepítéséhez, a rendszer az összes módosítás vissza lett állítva. (AZONOSÍTÓ: 4024) | Azure-ügynök manuális telepítése


## <a name="configuring-protection-group"></a>Védelmi csoport konfigurálása
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Védelmi csoportok konfigurálása | A DPM nem tudta felsorolni a védett számítógépen (védett számítógép neve) alkalmazás-összetevő | Kattintson a "Frissítés" konfigurálása védelmi csoport felhasználói felület képernyőn a megfelelő adatforrás/összetevő szinten |
| Védelmi csoportok konfigurálása | Nem sikerült beállítani a védelmet | Ha a védett kiszolgáló egy SQL server, ellenőrizze, hogy sysadmin szerepköri jogosultságok vannak-e megadva a védett számítógépen a system fióknak (NTAuthority\System) leírtak alapján [Ez a cikk](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)
| Védelmi csoportok konfigurálása | Nincs elegendő szabad hely a védelmi csoport számára a tárolókészletben | A lemezeket, amelyeket a tárolókészletbe való hozzáadásakor [nem tartalmazhatja a partíció](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Törölni egyetlen meglévő kötetet a lemezen, majd vegye fel a tárolókészlet|
| Házirend módosítása |A biztonsági mentési házirend nem módosítható. Hiba: Az aktuális művelet [0x29834] belső szolgáltatási hiba miatt sikertelen volt. Várjon egy kis ideig, majd ismételje meg a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |**OK:**<br/>Ez a hiba érkezett a biztonsági beállítások engedélyezve vannak, próbálja meg csökkenteni a megőrzési időtartam alatt a fent megadott minimális értékek és a (alatt MAB verzió 2.0.9052 és az Azure Backup server frissítési 1) nem támogatott verziója van. <br/>**Javasolt művelet:**<br/> Ebben az esetben állítsa be a megadott időszak minimális megőrzési (hét nap négy hétben-naponta, hetente, a havi vagy éves egy év három hétig) feletti megőrzési időszak házirend folytatásához kapcsolódó frissítések. Szükség esetén a kedvelt módszer a biztonságimásolat-készítő ügynök és az Azure Backup Server kihasználhatják a biztonsági frissítések lenne. |

## <a name="backup"></a>Biztonsági mentés
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Biztonsági mentés | A replika inkonzisztens | Győződjön meg arról, hogy automatikus consitency, jelölje be a beállítást a védelmi csoport varázsló be van kapcsolva. Az okok, illetve a replika inkonzisztenciája miatt vonatkozó további információkat talál [Itt](https://technet.microsoft.com/library/cc161593.aspx) <br> <ol><li> Esetén a rendszerállapot/operációs rendszer nélküli biztonsági mentést ellenőrizze, hogy a Windows Server biztonsági másolat van telepítve, vagy nem található a védett kiszolgálón </li><li> Ellenőrizze a helyet kapcsolatos problémát a DPM-tároló-készlet a DPM/MABS kiszolgálón, és szükség szerint tároló foglalása </li><li> Ellenőrizze a kötet árnyékmásolata szolgáltatás a védett kiszolgáló állapotát. Ha a letiltott állapotú beállítás kézi indításra, és indítsa el a szolgáltatást a kiszolgálón. Ezután térjen vissza a DPM/MABS konzolt, és elindítja a szinkronizálást és konzisztencia-ellenőrzést.</li></ol>|
| Biztonsági mentés | Váratlan hiba történt a feladat futása közben, az eszköz nem áll készen | **Ha a termék látható javasolt művelet nem működik**, <br> <ol><li>Az árnyékmásolat-tároló terület a védelmi csoportban található elemek a korlátlan beállítva, és a konzisztencia-ellenőrzés futtatása <br></li> (VAGY) <li>Próbálja meg törölni a meglévő védelmi csoportot, és újakat hozhat létre több – egyet minden egyes elemére</li></ol> |
| Biztonsági mentés | Ha biztonsági mentést csak a rendszerállapotról, ellenőrizze, ha van-e elég szabad hely a védett számítógépen a rendszerállapot biztonsági másolatának tárolásához | <ol><li>Győződjön meg arról, hogy telepítve van-e a WSB a védett számítógépen</li><li>Győződjön meg arról, hogy elegendő lemezterület található a védett számítógépen a rendszerállapot: Ennek legegyszerűbb módja a nyissa meg a védett számítógépen nyissa meg a WSB-t és a beállítások haladjon végig a, majd válassza ki az operációs rendszer nélküli Helyreállítás. A felhasználói felület majd megtudhatja, hogy mennyi helyre szükség a. Nyissa meg a WSB -> helyi biztonsági mentés -> biztonsági mentési ütemezés válassza ki a biztonsági mentés konfigurációja -> -> a teljes kiszolgáló (méret jelenik meg). Ez a méret használni az ellenőrzéshez.</li></ol>
| Biztonsági mentés | Online helyreállítási pont létrehozása nem sikerült | Ha a hibaüzenet szerint "a Windows Azure Backup szolgáltatás ügynöke nem tudta létrehozni a pillanatképet készítenie a kijelölt kötetről", próbálja meg növelni a területet az replika és a helyreállítási pont kötetén.
| Biztonsági mentés | Online helyreállítási pont létrehozása nem sikerült | Ha a hibaüzenet szerint "A Windows Azure Backup szolgáltatás ügynökének nem lehet csatlakozni az OBEngine szolgáltatáshoz", győződjön meg arról, hogy az OBEngine létezik-e a számítógépen futó szolgáltatások közül. Ha az OBEngine szolgáltatás nem fut a "net start OBEngine" parancs segítségével indítsa el az OBEngine szolgáltatáshoz.
| Biztonsági mentés | Online helyreállítási pont létrehozása nem sikerült | Ha a hibaüzenet szerint "a titkosítási jelszót ehhez a kiszolgálóhoz nincs beállítva. Állítsa be a titkosítás jelszavát"próbálja konfigurálása a titkosítás jelszavát. Ha a hiba, <br> <ol><li>Ellenőrizze, hogy az ideiglenes helyet létezik-e vagy sem. Léteznie kell a helyre, a beállításjegyzék HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config "ScratchLocation" néven szerepel.</li><li> Ha az ideiglenes létezik, próbálja újraregisztrálása a régi jelszót használja. **Amikor konfigurál a titkosítás jelszavát, előbb mentse a munkafüzetet egy biztonságos helyre**</li><ol>
| Biztonsági mentés | A BMR biztonsági mentés hibája | Ha a BMR mérete túl nagy, helyezze át néhány alkalmazás fájlt az operációs rendszer meghajtóra próbálja meg újra |
| Biztonsági mentés | Ismételt védelmével VMWare virtuális gép új MABS kiszolgálón nem szerepelnek a adhatók hozzá. | Egy régi, elavult MABS kiszolgáló VMWare tulajdonságainak vannak hivatkozott. A probléma megoldásához: "Összefoglalás" fülre, majd az "Egyéni attribútum" nyissa meg a VCenter (SC-VMM egyenértékű).  Törölje a régi MABS kiszolgálónév "DPMServer" értékét.  Az új MABS kiszolgálóra visszalép és módosítja a old.  Használja a "Frissítés" gombra, a virtuális gép számára jelenik meg a jelölőnégyzet védelmét hozzáadandó elérhetőként. |
| Biztonsági mentés | Hiba történt a megosztott fájlok/mappák használata közben: | Próbálja meg módosítani a víruskereső beállításokat javasolt [Itt](https://technet.microsoft.com/library/hh757911.aspx)|
| Biztonsági mentés | Online helyreállítási pontjainak létrehozási feladatai VMware virtuális gép nem sikerül. A DPM hibát kapott VMware változáskövetés információk beolvasásának megkísérlése során. Hibakód - FileFaultFault (azonosító: 33621) |  1. Alaphelyzetbe állítja a ctk a VMware rendszerben, az érintett virtuális gépekhez <br/> Ellenőrizze, hogy a független lemez nincs érvényben, a VMware rendszerben <br/> Állítsa le az érintett virtuális gépek védelmét, és a frissítés gombra védelmének újbóli beállításához <br/> Futtassa egy másolatot kap az érintett virtuális gépekhez|


## <a name="change-passphrase"></a>Jelszó módosítása
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Jelszó módosítása |Biztonsági megadott PIN-kód nem megfelelő. Adja meg a megfelelő biztonsági PIN-kódot a művelet végrehajtásához. |**OK:**<br/> Ez a hiba előre érvénytelen vagy lejárt biztonsági PIN-kódot kell megadni (például a jelszó módosítása) kritikus művelet végrehajtása közben. <br/>**Javasolt művelet:**<br/> A művelet befejezéséhez adjon meg érvényes biztonsági PIN-kódot. Ahhoz, hogy a PIN-kódot, jelentkezzen be Azure-portálon, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód segítségével módosíthatja a jelszót. |
| Jelszó módosítása |A művelet sikertelen volt. AZONOSÍTÓ: 120002 |**OK:**<br/>Ez a hiba elérhető lesz, ha a biztonsági beállítások engedélyezve vannak, megpróbálja módosítani a jelszót, és nem támogatott verziójú.<br/>**Javasolt művelet:**<br/> A jelszó módosításához frissíteni kell biztonságimásolat-készítő ügynök minimális 2.0.9052 és az Azure Backup server minimális Update 1, majd adjon meg érvényes biztonsági PIN-kód minimális verziójára. Ahhoz, hogy a PIN-kódot, jelentkezzen be Azure-portálon, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód segítségével módosíthatja a jelszót. |


## <a name="configure-email-notifications"></a>E-mail értesítések beállítása
| Művelet | Hiba legutolsó részletes adatai | Megkerülő megoldás |
| --- | --- | --- |
| Kísérlet történt az Office365 fiókkal e-mail értesítések beállítása. | első Hibaazonosító: 2013| **OK:**<br/> Próbálja használni az Office 365-fiókkal <br/> **Javasolt művelet:**<br/> Először is győződjön meg arról, hogy az, hogy "Engedélyezése névtelen továbbító a egy fogadási összekötőn" a DPM-kiszolgáló beállítása az Exchange. Itt egy hivatkozás megtalálható a fentiek konfigurálásával: http://technet.microsoft.com/en-us/library/bb232021.aspx <br/> Ha nem használ egy belső SMTP-továbbító, és be kell állítania az Office 365-kiszolgálót használ, állíthatja be az IIS kell lennie a továbbító ehhez. <br/> Kell tennie a SMTP o365 IIS https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx to setup IIS to relay to O365-sel továbbítják a DPM-kiszolgáló konfigurálása <br/> Fontos megjegyzés: A lépésben 3 g -> -> ii, használjon user@domain.com formátumú és nem tartomány\felhasználó <br/> Pont a DPM a helyi kiszolgálónév használandó SMTP-kiszolgálóval, port 587 és majd a felhasználói e-mailek, az e-mailt kell származnia. <br/> A felhasználónevet és jelszót a DPM SMTP beállítási oldalon kell lennie a tartományban, a DPM a nem tartományi fiók. <br/> Megjegyzés: Ha az SMTP-kiszolgáló címére módosítása a módosítást az új beállítások, a beállítások bezárásához és nyissa meg újból, hogy tükrözi az új érték.  Egyszerűen módosítása és tesztelése nem mindig érvénybe az új beállítások, így tesztelése az ajánlott eljárás. <br/> A folyamat során bármikor a DPM-konzol bezárása, és a következő beállításkulcsok szerkesztésével törölheti ezek a beállítások:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Törölje a SMTPPassword és SMTPUserName kulcsokat. <br/> Később is hozzáadhatja a felhasználói felület újra a amikor újra elindíthatja.
