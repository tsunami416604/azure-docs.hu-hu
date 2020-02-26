---
title: A hibrid biztonsági mentéseket védő biztonsági funkciók
description: Megtudhatja, hogyan teheti biztonságosabbá a biztonsági mentéseket a Azure Backup biztonsági funkciói segítségével
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 7213f26493a118c2cb32f8f9935b4954176b99a2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586393"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Biztonsági funkciók a Azure Backupt használó hibrid biztonsági másolatok védelméhez

A biztonsági problémákkal, például a kártevővel, a ransomware és a behatolással kapcsolatos problémák egyre nagyobbak. Ezek a biztonsági problémák költségesek lehetnek a pénz és az adatmennyiség tekintetében. Az ilyen támadások elleni védelem érdekében Azure Backup mostantól biztonsági funkciókat biztosít a hibrid biztonsági másolatok védelméhez. Ez a cikk bemutatja, hogyan engedélyezheti és használhatja ezeket a szolgáltatásokat Azure Recovery Services-ügynök és-Azure Backup Server használatával. Ezek a szolgáltatások a következők:

- **Megelőzés**. A rendszer egy további hitelesítési réteget ad hozzá, amikor egy kritikus művelet, például a jelszó módosítása történik. Ez az ellenőrzés biztosítja, hogy ezeket a műveleteket csak azok a felhasználók tudják végrehajtani, akik rendelkeznek érvényes Azure-beli hitelesítő adatokkal.
- **Riasztás**. A rendszer értesítést küld az előfizetés-rendszergazdának, amikor egy kritikus művelet, például a biztonsági mentési adattörlés történik. Ez az e-mail biztosítja, hogy a felhasználó gyorsan értesüljön az ilyen műveletekről.
- **Helyreállítás**. A törölt biztonsági mentési adatok a törlés időpontjától számítva további 14 napig megmaradnak. Ez biztosítja az adathelyreállítást egy adott időszakon belül, így a támadás esetén sem történik adatvesztés. Emellett a rendszer a minimális helyreállítási pontok számát is megőrzi a sérült információk védelme érdekében.

> [!NOTE]
> A biztonsági funkciókat nem szabad engedélyezni, ha az infrastruktúra-szolgáltatás (IaaS) virtuális gép biztonsági mentését használja. Ezek a funkciók még nem állnak rendelkezésre a IaaS virtuális gépek biztonsági mentéséhez, így azok nem lesznek hatással. A biztonsági funkciókat csak akkor kell engedélyezni, ha a következőket használja: <br/>
>  * **Azure Backup ügynök**. Az ügynök minimális verziószáma 2.0.9052. Miután engedélyezte ezeket a szolgáltatásokat, a kritikus műveletek végrehajtásához frissítsen erre az ügynök-verzióra. <br/>
>  * **Azure Backup Server**. A minimális Azure Backup ügynök verziója a Azure Backup Server Update 1 2.0.9052. <br/>
>  * **System Center Data Protection Manager**. A minimális Azure Backup ügynök verziója 2.0.9052 Data Protection Manager 2012 R2 UR12 vagy Data Protection Manager 2016 UR2. <br/>


> [!NOTE]
> Ezek a szolgáltatások csak Recovery Services-tárolóhoz érhetők el. Az újonnan létrehozott Recovery Services-tárolók alapértelmezés szerint engedélyezve vannak ezekkel a szolgáltatásokkal. A meglévő Recovery Services-tárolók esetében a felhasználók a következő szakaszban említett lépések végrehajtásával engedélyezhetik ezeket a funkciókat. A szolgáltatások engedélyezése után a rendszer az összes Recovery Services ügynök számítógépére, Azure Backup Server példányára, valamint a tárolóban regisztrált Data Protection Manager kiszolgálóra vonatkozik. Ha engedélyezi ezt a beállítást, a rendszer egyszeri műveletet végez, és ezek engedélyezése után nem tilthatja le ezeket a funkciókat.
>

## <a name="enable-security-features"></a>Biztonsági funkciók engedélyezése

Ha Recovery Services-tárolót hoz létre, az összes biztonsági funkciót használhatja. Ha egy meglévő tárolóval dolgozik, a következő lépésekkel engedélyezheti a biztonsági funkciókat:

1. Az Azure-beli hitelesítő adataival jelentkezzen be a Azure Portalba.
2. Válassza a **Tallózás**lehetőséget, és írja be a **Recovery Services**.

    ![Képernyőkép Azure Portal tallózási lehetőségről](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A Recovery Services-tárolók listája megjelenik. A listából válassza ki a tárolót. Megnyílik a kiválasztott tároló irányítópultja.
3. A tár alatt megjelenő elemek listájában a **Beállítások**területen kattintson a **Tulajdonságok**elemre.

    ![Képernyőkép a Recovery Services tároló lehetőségeiről](./media/backup-azure-security-feature/vault-list-properties.png)
4. A **biztonsági beállítások**területen kattintson a **frissítés**elemre.

    ![Képernyőkép a Recovery Services tároló tulajdonságairól](./media/backup-azure-security-feature/security-settings-update.png)

    A frissítés hivatkozásra kattintva megnyílik a **biztonsági beállítások** panel, amely összefoglalja a szolgáltatásokat, és lehetővé teszi azok engedélyezését.
5. A legördülő listából **konfigurálta az azure multi-Factor Authentication?** beállítást, és erősítse meg, hogy engedélyezve van-e az [Azure multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Ha engedélyezve van, a rendszer arra kéri, hogy végezzen hitelesítést egy másik eszközről (például egy mobiltelefonról) a Azure Portalba való bejelentkezéskor.

   Ha kritikus műveleteket végez a biztonsági mentésben, meg kell adnia egy biztonsági PIN-kódot, amely elérhető a Azure Portal. Az Azure Multi-Factor Authentication engedélyezése biztonsági réteget ad. Csak az érvényes Azure-beli hitelesítő adatokkal rendelkező és egy második eszközről hitelesített jogosult felhasználók férhetnek hozzá a Azure Portalhoz.
6. A biztonsági beállítások mentéséhez válassza az **Engedélyezés** lehetőséget, majd kattintson a **Mentés**gombra. Ha az előző lépésben az **Azure multi-Factor Authentication? listában beállított** értéket választotta, válassza ki az **Engedélyezés** lehetőséget.

    ![A biztonsági beállítások képernyőképe](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Törölt biztonsági mentési adatok helyreállítása

A biztonsági mentés további 14 napig megőrzi a törölt biztonsági másolati adatait, és nem törli azonnal, ha a **biztonsági mentés törlése művelettel leállítja a biztonsági** mentést. Ha a 14 napos időszakban szeretné visszaállítani ezeket az adatfájlokat, hajtsa végre a következő lépéseket, attól függően, hogy mit használ:

Az **Azure Recovery Services Agent** felhasználói számára:

1. Ha az a számítógép, amelyen a biztonsági mentések történnek, továbbra is elérhetővé válik a törölt adatforrások védelme, és az [adatok helyreállítása ugyanarra a gépre](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) az Azure Recovery Servicesban, hogy az összes régi helyreállítási pontból helyreállítható legyen.
2. Ha ez a számítógép nem érhető el, használja a [helyreállítás másik gépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) , hogy egy másik Azure Recovery Services számítógépet használjon az adatok lekéréséhez.

**Azure Backup Server** felhasználók számára:

1. Ha a kiszolgáló, amelyen a biztonsági mentések történnek, továbbra is elérhetővé válik, ellenőrizze újra a törölt adatforrásokat, és használja az **adatok helyreállítása** funkciót az összes régi helyreállítási pontból való helyreállításhoz.
2. Ha ez a kiszolgáló nem érhető el, az adatok [helyreállításához használja a másik Azure Backup Server adatokat](backup-azure-alternate-dpm-server.md) egy másik Azure Backup Server példány használatával.

**Data Protection Manager** felhasználók számára:

1. Ha a kiszolgáló, amelyen a biztonsági mentések történnek, továbbra is elérhetővé válik, ellenőrizze újra a törölt adatforrásokat, és használja az **adatok helyreállítása** funkciót az összes régi helyreállítási pontból való helyreállításhoz.
2. Ha ez a kiszolgáló nem érhető el, használja a [külső DPM hozzáadása](backup-azure-alternate-dpm-server.md) lehetőséget egy másik Data Protection Manager-kiszolgáló használatára az adatkéréshez.

## <a name="prevent-attacks"></a>Támadások megelőzése

A rendszer ellenőrzi, hogy csak az érvényes felhasználók hajthatnak végre különböző műveleteket. Ezek közé tartozik a további hitelesítési réteg hozzáadása, valamint a minimális megőrzési időtartam fenntartása helyreállítási célokra.

### <a name="authentication-to-perform-critical-operations"></a>Hitelesítés a kritikus műveletek végrehajtásához

Ha további hitelesítési réteget ad hozzá a kritikus műveletekhez, a rendszer megkéri, hogy adjon meg egy biztonsági PIN-kódot, ha az adatok törlése és a **hozzáférési kód módosítása** művelet végrehajtásakor **leállítja a védelmet** .

> [!NOTE]
>
> A biztonsági PIN-kód jelenleg nem támogatott a DPM-és MABS- **adattörlési adattörlési szolgáltatással való védelem leállítására** .

A PIN-kód fogadása:

1. Jelentkezzen be az Azure portálra.
2. Tallózással keresse meg **Recovery Services** tár > beállítások > a **Tulajdonságok** **lehetőséget** .
3. A **biztonsági PIN-kód**alatt kattintson a **előállítás**elemre. Ekkor megnyílik egy panel, amely tartalmazza az Azure Recovery Services Agent felhasználói felületén beírni kívánt PIN-kódot.
    Ez a PIN-kód csak öt percre érvényes, és az adott időszak után automatikusan létrejön.

### <a name="maintain-a-minimum-retention-range"></a>Minimális megőrzési időtartam fenntartása

Annak biztosítása érdekében, hogy mindig érvényes számú helyreállítási pont álljon rendelkezésre, a következő ellenőrzéseket adta hozzá:

- A napi megőrzéshez legalább **hét** napos megőrzést kell végezni.
- A heti megőrzéshez legalább **négy** hetes megőrzést kell végrehajtani.
- A havi megőrzéshez legalább **három** hónapos megőrzést kell végrehajtani.
- Az évenkénti megőrzéshez legalább **egy** évig meg kell tartani a megőrzést.

## <a name="notifications-for-critical-operations"></a>Kritikus műveletekre vonatkozó értesítések

Ha kritikus műveletet hajt végre, az előfizetés rendszergazdája e-mailben értesítést küld a művelet részleteiről. Az értesítésekhez további e-mail-címzetteket is beállíthat a Azure Portal használatával.

A cikkben említett biztonsági funkciók védelmi mechanizmusokat biztosítanak a megtámadott támadásokkal szemben. Ennél is fontosabb, hogy ha támadás történik, ezek a funkciók lehetővé teszik az adatok helyreállítását.

## <a name="troubleshooting-errors"></a>Hibaelhárítás

| Művelet | A hiba részletei | Megoldás: |
| --- | --- | --- |
| Szabályzat módosítása |Nem lehet módosítani a biztonsági mentési szabályzatot. Hiba: az aktuális művelet végrehajtása egy belső szolgáltatáshiba ([0x29834]) miatt meghiúsult. Némi várakozás után próbálja megismételni a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatához. |**Ok:**<br/>Ez a hiba akkor jelenik meg, ha a biztonsági beállítások engedélyezve vannak, ezért a fent megadott minimális értékeknél kevesebb megőrzési tartományt próbál meg csökkenteni (a támogatott verziók a cikk első megjegyzésében vannak megadva). <br/>**Javasolt művelet:**<br/> Ebben az esetben meg kell határoznia a megőrzési időtartamot a megadott minimális megőrzési időtartam (napi hét nap, heti, hetente három hét, havi vagy egy év esetében évente), a szabályzatokkal kapcsolatos frissítések folytatásához. Ha szeretné, az előnyben részesített módszer a Backup ügynök, a Azure Backup Server és/vagy a DPM frissítése az összes biztonsági frissítés kihasználása érdekében. |
| Hozzáférési kód módosítása |A megadott biztonsági PIN-kód helytelen. (AZONOSÍTÓ: 100130) A művelet végrehajtásához adja meg a helyes biztonsági PIN-kódot. |**Ok:**<br/> Ez a hiba akkor jelenik meg, ha a kritikus művelet végrehajtása közben érvénytelen vagy lejárt biztonsági PIN-kódot ad meg (például a jelszó módosítása). <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be Azure Portal, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód létrehozásához. Használja ezt a PIN-kódot a jelszó módosításához. |
| Hozzáférési kód módosítása |A művelet sikertelen volt. ID: 120002 |**Ok:**<br/>Ez a hiba akkor jelenik meg, ha a biztonsági beállítások engedélyezve vannak, és nem támogatott verziót próbál módosítani (a cikk első megjegyzésében megadott érvényes verziók).<br/>**Javasolt művelet:**<br/> A hozzáférési kód módosításához először frissítenie kell a Backup ügynököt a minimálisan szükséges minimális 2.0.9052, Azure Backup-kiszolgálóval az 1. és/vagy DPM minimális DPM 2012 R2 UR12 vagy DPM 2016 UR2 (letöltési hivatkozások alább), majd érvényes biztonsági PIN-kódot kell megadnia. A PIN-kód beszerzéséhez jelentkezzen be Azure Portal, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód létrehozásához. Használja ezt a PIN-kódot a jelszó módosításához. |

## <a name="next-steps"></a>Következő lépések

- Az Azure Recovery Services-tároló használatának [első lépései](backup-azure-vms-first-look-arm.md) a funkciók engedélyezéséhez.
- [Töltse le a legújabb Azure Recovery Services-ügynököt](https://aka.ms/azurebackup_agent) , hogy megvédje a Windows rendszerű számítógépeket, és védelmet nyújtson biztonsági mentési adatainak a támadásokkal szemben.
- [A legújabb Azure Backup Server letöltésével](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) megvédheti a munkaterheléseket, és védelmet biztosíthat a biztonsági mentési adatoknak a támadásokkal szemben.
- [Töltse le a UR12 for System center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) , vagy [töltse le a system Center 2016 Data Protection Manager UR2](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) , hogy megvédje a munkaterheléseket, és védelmet nyújtson a biztonsági mentési adatoknak a támadásokkal szemben.
