---
title: A hibrid biztonsági mentések védelmét szolgáló biztonsági funkciók
description: Megtudhatja, hogy miként használhatja az Azure Backup biztonsági funkcióit a biztonsági mentések biztonságosabbá tétele érdekében
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586393"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Az Azure Backup biztonsági mentést használó hibrid biztonsági mentések védelmére szolgáló biztonsági funkciók

A biztonsági problémákkal kapcsolatos aggodalmak, például a rosszindulatú programok, a zsarolóprogramok és a behatolás oka egyre nő. Ezek a biztonsági kérdések költségesek lehetnek mind a pénz, mind az adatok tekintetében. Az ilyen támadások elleni védelem érdekében az Azure Backup most antól biztonsági funkciókat biztosít a hibrid biztonsági mentések védelme érdekében. Ez a cikk ismerteti, hogyan engedélyezheti és használhatja ezeket a funkciókat egy Azure Recovery Services-ügynök és az Azure Backup Server használatával. Ezek a szolgáltatások a következők:

- **Megelőzés**. A rendszer egy további hitelesítési réteget ad hozzá, amikor kritikus műveletet hajtanak végre, például egy jelszót módosítanak. Ez az ellenőrzés biztosítja, hogy az ilyen műveleteket csak érvényes Azure-hitelesítő adatokkal rendelkező felhasználók hajthassák végre.
- **Riasztás .** A rendszer e-mailértesítést küld az előfizetés rendszergazdájának, ha kritikus művelet, például biztonsági mentési adatok törlése történik. Ez az e-mail biztosítja, hogy a felhasználó gyorsan értesítést kapjon az ilyen műveletekről.
- **Helyreállítás**. A törölt biztonsági mentési adatok a törlés napjától számított további 14 napig maradnak meg. Ez biztosítja az adatok helyrehozhatóságát egy adott időszakon belül, így nincs adatvesztés akkor is, ha támadás történik. Emellett nagyobb számú minimális helyreállítási pontot tartanak fenn a sérült adatok elleni védelem érdekében.

> [!NOTE]
> Biztonsági funkciók nem engedélyezhető, ha az infrastruktúra szolgáltatás (IaaS) virtuális gép biztonsági mentése. Ezek a funkciók még nem érhetők el az IaaS virtuális gépek biztonsági mentéséhez, így az engedélyezésük nem lesz hatással. A biztonsági funkciókat csak akkor szabad engedélyezni, ha a következőket használja: <br/>
>  * **Az Azure Backup-ügynök**. Minimális ügynök verzió 2.0.9052. Miután engedélyezte ezeket a szolgáltatásokat, frissítenie kell erre az ügynökverzióra a kritikus műveletek végrehajtásához. <br/>
>  * **Az Azure Backup Server kiszolgáló**. Minimális Azure Backup ügynök 2.0.9052-es verziója az Azure Backup Server 1-es frissítésével. <br/>
>  * **System Center Adatvédelmi vezetője**. Minimális Azure Backup ügynök 2.0.9052-es verziója a Data Protection Manager 2012 R2 UR12 vagy a Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Ezek a funkciók csak a Recovery Services-tárolóban érhetők el. Az újonnan létrehozott Helyreállítási szolgáltatások tárolói alapértelmezés szerint engedélyezve vannak ezekkel a szolgáltatásokkal. A meglévő Helyreállítási szolgáltatások tárolók esetében a felhasználók a következő szakaszban ismertetett lépések segítségével engedélyezik ezeket a szolgáltatásokat. Miután a funkciók engedélyezve vannak, a Helyreállítási szolgáltatások ügynöke számítógépekre, az Azure Backup Server-példányokra és a tárolóban regisztrált Adatvédelmi kezelő kiszolgálókra vonatkoznak. A beállítás engedélyezése egyszeri művelet, és ezeket a szolgáltatásokat az engedélyezésük után nem tilthatja le.
>

## <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése

Ha helyreállítási szolgáltatások tárolót hoz létre, használhatja az összes biztonsági funkciót. Ha meglévő tárolóval dolgozik, engedélyezze a biztonsági funkciókat az alábbi lépésekkel:

1. Jelentkezzen be az Azure Portalra az Azure-hitelesítő adatok használatával.
2. Válassza a **Tallózás**lehetőséget, és írja be a **Helyreállítási szolgáltatások parancsot.**

    ![Képernyőkép az Azure Portal Tallózás beállításáról](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A Recovery Services-tárolók listája megjelenik. Ebből a listából válasszon ki egy tárolót. Megnyílik a kiválasztott tároló irányítópultja.
3. A tároló alatt megjelenő elemek listájában a **Beállítások**csoportban kattintson a **Tulajdonságok gombra.**

    ![Képernyőkép a Recovery Services-tároló beállításairól](./media/backup-azure-security-feature/vault-list-properties.png)
4. A **Biztonsági beállítások csoportban**kattintson a **Frissítés gombra.**

    ![Képernyőkép a Recovery Services-tároló tulajdonságairól](./media/backup-azure-security-feature/security-settings-update.png)

    A frissítési hivatkozás megnyitja a **Biztonsági beállítások** panelt, amely a szolgáltatások összegzését tartalmazza, és lehetővé teszi azok engedélyezését.
5. A legördülő listából **konfigurálta az Azure többtényezős hitelesítést?** [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) Ha engedélyezve van, a rendszer arra kéri, hogy egy másik eszközről (például egy mobiltelefonról) hitelesítse magát, miközben bejelentkezik az Azure Portalra.

   Ha kritikus műveleteket hajt végre a biztonsági mentésben, meg kell adnia egy biztonsági PIN-kódot, amely elérhető az Azure Portalon. Az Azure többtényezős hitelesítésének engedélyezése egy biztonsági réteget ad hozzá. Csak az érvényes Azure-hitelesítő adatokkal rendelkező és egy második eszközről hitelesített jogosult felhasználók férhetnek hozzá az Azure Portalhoz.
6. A biztonsági beállítások mentéséhez válassza az **Engedélyezés** és a **Mentés**gombra. Csak akkor válassza **az Engedélyezés** lehetőséget, ha kiválasztott egy értéket az Előző lépésben az **Azure többtényezős hitelesítés konfigurálása?** listában.

    ![Képernyőkép a biztonsági beállításokról](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Törölt biztonsági mentési adatok helyreállítása

A biztonsági mentés további 14 napig megőrzi a törölt biztonsági mentési adatokat, és nem törli azonnal, ha a **biztonsági mentési adatok törlésével való biztonsági mentés leállítása** művelet et hajt végre. Ha vissza szeretné állítani ezeket az adatokat a 14 napos időszakban, tegye a következő lépéseket attól függően, hogy mit használ:

**Az Azure Recovery Services ügynökfelhasználói** számára:

1. Ha a számítógép, ahol a biztonsági mentések történtek továbbra is elérhető, újra védje a törölt adatforrások, és használja az [adatok helyreállítása ugyanahhoz a géphez az](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) Azure Recovery Services, a régi helyreállítási pontok helyreállítása.
2. Ha ez a számítógép nem érhető el, használja [a Helyreállítás egy másik gépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) egy másik Azure Recovery Services-számítógép használatával az adatok leéséhez.

**Az Azure Backup Server** felhasználói számára:

1. Ha a kiszolgáló, ahol a biztonsági mentések történtek, továbbra is elérhető, védje újra a törölt adatforrásokat, és az **Adatok helyreállítása** szolgáltatással helyreállíthatja az összes régi helyreállítási pontot.
2. Ha ez a kiszolgáló nem érhető el, használja [az adatok helyreállítása egy másik Azure Backup Server](backup-azure-alternate-dpm-server.md) használatával egy másik Azure Backup Server-példány t i.

**Az Adatvédelmi menedzser** felhasználói számára:

1. Ha a kiszolgáló, ahol a biztonsági mentések történtek, továbbra is elérhető, védje újra a törölt adatforrásokat, és az **Adatok helyreállítása** szolgáltatással helyreállíthatja az összes régi helyreállítási pontot.
2. Ha ez a kiszolgáló nem érhető el, a [Külső DPM hozzáadása](backup-azure-alternate-dpm-server.md) segédprogram segítségével egy másik Adatvédelmi kezelő kiszolgálót használjon az adatok leéséhez.

## <a name="prevent-attacks"></a>Támadások megelőzése

A megadott ellenőrzések biztosítják, hogy csak az érvényes felhasználók hajthassanak végre különböző műveleteket. Ezek közé tartozik egy további hitelesítési réteg hozzáadása, és egy minimális megőrzési tartomány fenntartása helyreállítási célokra.

### <a name="authentication-to-perform-critical-operations"></a>Hitelesítés a kritikus műveletek végrehajtásához

A kritikus műveletekhez egy további hitelesítési réteg hozzáadásaként a rendszer kéri, hogy adjon meg egy biztonsági PIN-kódot, amikor a Stop Protection-t az Adatok törlése és **a Jelszó módosítása** **műveletekkel** hajtja végre.

> [!NOTE]
>
> Jelenleg a biztonsági tű nem támogatott a Stop Protection esetében a DPM és az MABS **adatainak törlése** esetén.

A PIN-kód fogadása:

1. Jelentkezzen be az Azure portálra.
2. Tallózással keresse meg a Helyreállítási szolgáltatások > **tárolójának beállításai** > **nak tulajdonságait.** **Recovery Services vault**
3. A **Biztonsági PIN-kód csoportban**kattintson a **Létrehozás gombra.** Ez megnyit egy panelt, amely tartalmazza az Azure Recovery Services ügynök felhasználói felületén megadandó PIN-kódot.
    Ez a PIN-kód csak öt percig érvényes, és az adott időszak után automatikusan létrejön.

### <a name="maintain-a-minimum-retention-range"></a>Minimális megőrzési tartomány fenntartása

Annak érdekében, hogy mindig érvényes számú helyreállítási pont áll rendelkezésre, a következő ellenőrzésekkel egészült ki:

- A napi megőrzéshez legalább **hét** napos megőrzést kell végezni.
- A heti visszatartás, legalább **négy** hét retenciós kell tenni.
- A havi megőrzés, legalább **három** hónapos megőrzési kell tenni.
- Az éves visszatartás esetében legalább **egy** éves visszatartást kell végezni.

## <a name="notifications-for-critical-operations"></a>Értesítések a kritikus műveletekről

Kritikus művelet végrehajtásakor általában az előfizetés rendszergazdája e-mailértesítést kap a művelet részleteivel. Az Azure Portal használatával további e-mail címzetteket konfigurálhat ezekhez az értesítésekhez.

Az ebben a cikkben említett biztonsági funkciók védelmi mechanizmusokat biztosítanak a célzott támadások ellen. Ennél is fontosabb, ha támadás történik, ezek a funkciók lehetővé teszik az adatok helyreállítását.

## <a name="troubleshooting-errors"></a>Hibaelhárítás

| Művelet | A hiba részletei | Megoldás: |
| --- | --- | --- |
| Házirend-módosítás |A biztonsági mentési házirend nem módosítható. Hiba: Az aktuális művelet belső szolgáltatáshiba miatt nem sikerült (0x29834]. Próbálkozzon újra a művelettel valamikor. If the issue persists, please contact Microsoft support. (Az Azure Key Vault-művelet meghiúsult. Próbálja meg újból végrehajtani a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.) |**Ok:**<br/>Ez a hiba akkor jelentkezik, ha a biztonsági beállítások engedélyezve vannak, megpróbálja csökkenteni a megőrzési tartományt a fent megadott minimális értékek alá, és nem támogatott verziót tartalmaz (a támogatott verziók a cikk első feljegyzésében vannak megadva). <br/>**Ajánlott művelet:**<br/> Ebben az esetben a házirenddel kapcsolatos frissítések folytatásához állítsa be a megőrzési időszakot a megadott minimális megőrzési időszak felett (hét nap naponta, négy hét hetente, három hét havonta vagy egy év évente). Szükség esetén az előnyben részesített megközelítés a biztonsági mentési ügynök, az Azure Backup Server és/vagy a DPM UR frissítése lenne az összes biztonsági frissítés kihasználásához. |
| Jelszó módosítása |A megadott biztonsági PIN-kód helytelen. (ID: 100130) Adja meg a megfelelő biztonsági PIN-kódot a művelet végrehajtásához. |**Ok:**<br/> Ez a hiba akkor jelentkezik, ha érvénytelen vagy lejárt biztonsági PIN-kódot ad meg kritikus művelet végrehajtása közben (például a jelszó módosítása). <br/>**Ajánlott művelet:**<br/> A művelet végrehajtásához érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód leszerzéséhez jelentkezzen be az Azure Portalon, és keresse meg a Recovery Services-tárolót, > beállítások > tulajdonságok > biztonsági PIN-kód létrehozása. Ezzel a PIN-kóddal módosíthatja a jelszót. |
| Jelszó módosítása |A művelet nem sikerült. Azonosító: 120002 |**Ok:**<br/>Ez a hiba akkor jelentkezik, ha a biztonsági beállítások engedélyezve vannak, megpróbálja módosítani a jelszót, és a nem támogatott verziót használja (a cikk első feljegyzésében megadott érvényes verziókat).<br/>**Ajánlott művelet:**<br/> A jelszó módosításához először frissítenie kell a biztonsági másolat ügynökét minimum 2.0.9052-es verzióra, az Azure Backup kiszolgálót az 1., és/vagy a DPM-et minimális DPM 2012 R2 UR12 vagy DPM 2016 UR2-re (az alábbi letöltési hivatkozásokra), majd meg kell adnia az érvényes biztonsági PIN-kódot. A PIN-kód leszerzéséhez jelentkezzen be az Azure Portalon, és keresse meg a Recovery Services-tárolót, > beállítások > tulajdonságok > biztonsági PIN-kód létrehozása. Ezzel a PIN-kóddal módosíthatja a jelszót. |

## <a name="next-steps"></a>További lépések

- A szolgáltatások engedélyezéséhez ismerkedjen meg az [Azure Recovery Services-tárolóval.](backup-azure-vms-first-look-arm.md)
- [Töltse le a legújabb Azure Recovery Services-ügynököt a](https://aka.ms/azurebackup_agent) Windows-számítógépek védelme és a biztonsági mentési adatok támadások elleni védelme érdekében.
- [Töltse le a legújabb Azure Backup Server](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) t a számítási feladatok védelméhez és a biztonsági mentési adatok támadások elleni védelméhez.
- [Töltse le az UR12-t a System Center 2012 R2 Adatvédelmi kezelőhöz,](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) vagy [töltse le az UR2-t a System Center 2016 Adatvédelmi kezelőhöz,](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) hogy megvédje a munkaterheléseket és megvédje a biztonsági mentési adatokat a támadásoktól.
