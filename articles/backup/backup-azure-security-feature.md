---
title: "Az Azure Backup hibrid biztonsági mentések védelme érdekében biztonsági funkciók |} Microsoft Docs"
description: "Útmutató: Azure Backup szolgáltatás biztonsági szolgáltatások használatával biztonságosabbá teszi a biztonsági másolatok"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2017
ms.author: pajosh
ms.openlocfilehash: 8ef9ddc345fb553b93815022dc3e6a796cae8b3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Az Azure Backup hibrid biztonsági mentések védelme érdekében biztonsági funkciói
-Maillel kapcsolatos biztonsági problémák, például a kártevő szoftver, a nevű és a behatolás, növekednek. Lehet, hogy a biztonsági problémák költséges, pénzt és adatokat. Az ilyen támadások elleni védelmet, a Azure biztonsági mentés most hibrid biztonsági mentések védelme érdekében biztonsági szolgáltatásokat biztosítja. Ez a cikk bemutatja, hogyan adhat engedélyezése és ezek a funkciók használata az Azure Recovery Services Agent ügynök és az Azure Backup Server használatával. Ezek a funkciók a következők:

- **Megelőzési**. Egy további hitelesítési réteg kerül, amikor csak a kritikus például a jelszó módosítása a művelet. Ezen ellenőrzés, győződjön meg arról, hogy ezeket a műveleteket végzi el csak érvényes Azure hitelesítő adatokkal rendelkező felhasználók számára.
- **Riasztási**. E-mailben értesítést küld az előfizetés-adminisztrátor, amikor csak a kritikus például törlés, biztonsági mentési adatok a művelet. Az e-mailt biztosítja, hogy a felhasználó értesítést kap gyorsan ilyen műveleteket.
- **Helyreállítási**. Törölt biztonsági mentési adatok kiegészítő megőrzi a törlés 14 nap. Ez biztosítja az adatok helyreállíthatósága belül egy adott időszakban, így nincs adatvesztés nélküli, még akkor is, ha a támadás akkor fordul elő. Minimális helyreállítási pontok nagyobb számú is, amelyek védelmet biztosítanak a sérült adatok karbantartása.

> [!NOTE]
> Biztonsági funkciókat is engedélyeznie kell használata infrastruktúra (IaaS) szolgáltatás VM biztonsági mentéséhez. Ezeket a szolgáltatásokat még nem állnak rendelkezésre az infrastruktúra-szolgáltatási virtuális gép biztonsági mentése, így azokat nem semmilyen hatással lesz. Biztonsági funkciók csak a rendszer használata esetén engedélyezni kell: <br/>
>  * **Az Azure Backup szolgáltatás ügynökének**. Minimális ügynökverzió 2.0.9052. Miután engedélyezte a ezeket a funkciókat, frissítsen végrehajtott kulcsfontosságú műveleteket végrehajtani az ügynök verziója. <br/>
>  * **Az Azure Backup Server**. Azure Backup agent legalább 2.0.9052 Azure Backup Server 1. frissítés. <br/>
>  * **A System Center Data Protection Manager**. Azure Backup agent legalább 2.0.9052 a Data Protection Manager 2012 R2 UR12 vagy a Data Protection Manager 2016 UR2. <br/> 


> [!NOTE]
> Ezek a szolgáltatások csak Recovery Services-tároló érhetők el. Az újonnan létrehozott helyreállítási szolgáltatások tárolók ezek a szolgáltatások alapértelmezés szerint engedélyezve van. Meglévő Recovery Services-tárolók, a felhasználók engedélyezze ezeket a szolgáltatásokat a következő szakaszban leírt lépéseket. Miután a szolgáltatások engedélyezve vannak, azokat a Recovery Services agent számítógépekre vonatkozik, Azure Backup Server-példányokat, és a Data Protection Manager-kiszolgáló regisztrálva a tárolóban. A beállítás engedélyezése egyszeri művelet, és nem tiltható le ezeket a funkciókat Miután engedélyezte őket.
>

## <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése
Recovery Services-tároló létrehozásakor, a biztonsági szolgáltatásokat is használhatja. Ha egy meglévő tárolóhoz dolgozik, biztonsági szolgáltatások engedélyezése a következő lépések végrehajtásával:

1. Jelentkezzen be az Azure portál Azure hitelesítő adataival.
2. Válassza ki **Tallózás**, és írja be **Recovery Services**.

    ![Képernyőfelvétel az Azure portál tallózási lehetőséget](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A Recovery Services-tárolók listája megjelenik. Ebben a listában jelölje ki a tárolóban. Megnyílik a kiválasztott tároló irányítópultja.
3. Elemek a listában a létrehozott alatt jelenik meg a tárolóban **beállítások**, kattintson a **tulajdonságok**.

    ![Képernyőfelvétel a Recovery Services-tároló beállítások](./media/backup-azure-security-feature/vault-list-properties.png)
4. A **biztonsági beállítások**, kattintson a **frissítés**.

    ![Képernyőkép a Recovery Services-tároló tulajdonságok](./media/backup-azure-security-feature/security-settings-update.png)

    A frissítés a hivatkozás megnyitja a **biztonsági beállítások** panel, amelyen a szolgáltatások összegzését tartalmazza, és lehetővé teheti, hogy azokat.
5. A legördülő listából **állított Azure multi-factor Authentication?**, kiválaszthat egy értéket annak ellenőrzéséhez, ha engedélyezte a [Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication.md). Ha engedélyezve van, kell adnia egy másik eszközről (például egy mobiltelefon) hitelesítéséhez az Azure-portálon történő bejelentkezés során.

   A biztonsági mentés végrehajtott kulcsfontosságú műveleteket hajt végre, amikor meg kell adnia egy PIN-kód és az Azure portálon elérhető biztonsági. Azure multi-factor Authentication engedélyezése egy biztonsági réteget ad. Csak a feljogosított felhasználók érvényes Azure hitelesítő adatokkal, és egy második eszközről hitelesített, férhetnek hozzá az Azure-portálon.
6. Biztonsági beállítások mentéséhez válasszon **engedélyezése** kattintson **mentése**. Kiválaszthatja **engedélyezése** csak után kiválaszthat egy értéket a **állított Azure multi-factor Authentication?** listáját az előző lépésben.

    ![Képernyőkép a biztonsági beállítások](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>A törölt biztonsági mentési adatok helyreállítása
Biztonsági mentés további 14 napig őrzi meg törölt biztonsági mentési adatokat, és nem törlődik azonnal, ha a **Stop biztonsági mentés, a biztonsági mentési adatok** művelet. Az adatok helyreállítását a 14 napos időszak alatt, a következő lépésekkel, attól függően, hogy mi használ:

A **Azure Recovery Services Agent ügynököt** felhasználók:

1. Ha a számítógépen, ahol a biztonsági mentések folyamatban volt továbbra is elérhető, akkor [ugyanarra a számítógépre adatok helyreállítása](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) az Azure Recovery Services, a helyreállítás a régi helyreállítási pontokat.
2. Ez a számítógép nem érhető el, ha [helyreállítása egy másik gépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) egy másik Azure Recovery Services-számítógép segítségével az adatok beolvasása.

A **Azure Backup Server** felhasználók:

1. Ha a kiszolgáló, ahol a biztonsági mentések folyamatban volt továbbra is elérhető, a törölt adatforrásokhoz védelmének újbóli beállítását, és használja a **adatok helyreállítása** szolgáltatást, hogy a régi helyreállítási pontok helyreállíthatók.
2. Ez a kiszolgáló nem érhető el, ha [állíthat helyre adatokat egy másik Azure Backup Server](backup-azure-alternate-dpm-server.md) egy másik Azure Backup Server-példány használata az adatok eléréséhez.

A **Data Protection Manager** felhasználók:

1. Ha a kiszolgáló, ahol a biztonsági mentések folyamatban volt továbbra is elérhető, a törölt adatforrásokhoz védelmének újbóli beállítását, és használja a **adatok helyreállítása** szolgáltatást, hogy a régi helyreállítási pontok helyreállíthatók.
2. Ez a kiszolgáló nem érhető el, ha [külső DPM hozzáadása](backup-azure-alternate-dpm-server.md) egy másik Data Protection Manager-kiszolgáló segítségével az adatok beolvasása.

## <a name="prevent-attacks"></a>Támadások megelőzése érdekében
Győződjön meg arról, hogy csak akkor érvényes, ha a felhasználók különböző műveleteket hajthat végre ellenőrzések bővült. Ezek közé tartozik egy további hitelesítési réteggel hozzáadása és karbantartása minimális megőrzési időtartamot helyreállítási célokra.

### <a name="authentication-to-perform-critical-operations"></a>Hitelesítés kritikus műveletek végrehajtásához
Hajt végre egy biztonsági PIN-kód megadását kéri egy további hitelesítési végrehajtott kulcsfontosságú műveleteket az réteggel való hozzáadásának részeként **védelem kikapcsolása az adatok** és **Módosítsa jelszavát** műveletek.

A PIN-kód fogadására:

1. Jelentkezzen be az Azure portálra.
2. Keresse meg a **Recovery Services-tároló** > **beállítások** > **tulajdonságok**.
3. A **biztonsági PIN-kód**, kattintson a **Generate**. Ekkor megnyílik egy panel, amely tartalmazza a PIN-kódot kell megadni az Azure Recovery Services agent felhasználói felületen.
    A PIN-kód érvényes csak 5 percig, és automatikusan lekérdezi létrehozott az időszak elteltével.

### <a name="maintain-a-minimum-retention-range"></a>A minimális megőrzési tartomány karbantartása
Győződjön meg arról, hogy számos mindig érvényes helyreállítási pont érhető el, hogy a következő ellenőrzések lettek hozzáadva:

- A napi megőrzési, legalább **hét** napnyi megőrzési el kell végezni.
- A heti megőrzési, legalább **négy** hetes megőrzési el kell végezni.
- A havi megőrzési, legalább **három** hónapos megőrzéssel el kell végezni.
- Az éves megőrzési, legalább **egy** adatmegőrzési év el kell végezni.

## <a name="notifications-for-critical-operations"></a>A kritikus műveletek értesítések
Általában a kritikus műveletet hajtja végre, ha az előfizetés-adminisztrátor küldött működésével kapcsolatos részleteket az e-mailben értesítést. Az értesítések címzettjeit további e-mail konfigurálhatja az Azure portál használatával.

A cikkben említett biztonsági funkciók célzott támadások elleni védelmi mechanizmust biztosít. Ennél is fontosabb a támadás akkor fordul elő, ha ezeket a szolgáltatásokat biztosítanak az adatok helyreállíthatók.

## <a name="troubleshooting-errors"></a>Kapcsolatos hibák elhárítása
| Művelet | Hiba legutolsó részletes adatai | Megoldás: |
| --- | --- | --- |
| Házirend módosítása |A biztonsági mentési házirend nem módosítható. Hiba: Az aktuális művelet [0x29834] belső szolgáltatási hiba miatt sikertelen volt. Várjon egy kis ideig, majd ismételje meg a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |**OK:**<br/>Ez a hiba elérhető lesz, ha a biztonsági beállítások engedélyezve vannak, próbálja meg csökkenteni a megőrzési időtartam alatt a fent megadott minimális értékek, és nem támogatott verziójú (az első Megjegyzés: Ez a cikk a támogatott verziók vannak megadva). <br/>**Javasolt művelet:**<br/> Ebben az esetben állítsa be a megadott időszak minimális megőrzési (hét nap négy hétben-naponta, hetente, a havi vagy éves egy év három hétig) feletti megőrzési időszak házirend folytatásához kapcsolódó frissítések. Szükség esetén kedvelt módszer lehet frissíteni a biztonságimásolat-készítő ügynök, az Azure Backup Server és/vagy a DPM UR használja ki a biztonsági frissítéseket. |
| Jelszó módosítása |Biztonsági megadott PIN-kód nem megfelelő. (AZONOSÍTÓ: 100130) Adja meg a megfelelő biztonsági PIN-kódot a művelet végrehajtásához. |**OK:**<br/> Ez a hiba előre érvénytelen vagy lejárt biztonsági PIN-kódot kell megadni (például a jelszó módosítása) kritikus művelet végrehajtása közben. <br/>**Javasolt művelet:**<br/> A művelet befejezéséhez adjon meg érvényes biztonsági PIN-kódot. Ahhoz, hogy a PIN-kódot, jelentkezzen be Azure-portálon, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód segítségével módosíthatja a jelszót. |
| Jelszó módosítása |A művelet sikertelen volt. AZONOSÍTÓ: 120002 |**OK:**<br/>Ez a hiba származik, amikor a biztonsági beállítások engedélyezve vannak, megpróbálja módosítani a jelszót, és nem támogatott verziójú (Ez a cikk első megjegyzés-ben megadott érvényes verzió).<br/>**Javasolt művelet:**<br/> A jelszó módosításához frissíteni kell biztonságimásolat-készítő ügynök minimális verziójára minimális 2.0.9052, az Azure Backup server minimális Update 1, és/vagy a DPM minimális DPM 2012 R2 UR12 vagy a DPM 2016 UR2 (Letöltés a lenti hivatkozásokra kattintva), majd írjon be érvényes biztonsági PIN-kódot. Ahhoz, hogy a PIN-kódot, jelentkezzen be Azure-portálon, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód segítségével módosíthatja a jelszót. |

## <a name="next-steps"></a>Következő lépések
* [Ismerkedés az Azure Recovery Services-tároló](backup-azure-vms-first-look-arm.md) a funkciók engedélyezéséhez.
* [Töltse le a legújabb Azure Recovery Services Agent ügynököt](http://aka.ms/azurebackup_agent) Windows rendszerű számítógépek védelmét, és a biztonsági mentési adatok támadások elleni védelme érdekében.
* [Töltse le a legújabb Azure Backup Server](https://aka.ms/latest_azurebackupserver) munkaterhelések védelmét, és a biztonsági mentési adatok támadások elleni védelme érdekében.
* [Töltse le a System Center 2012 R2 Data Protection Manager UR12](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) vagy [töltse le a System Center 2016 Data Protection Manager UR2](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) munkaterhelések védelmét, és a biztonsági mentési adatok támadások elleni védelme érdekében.
