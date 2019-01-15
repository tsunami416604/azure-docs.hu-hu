---
title: Biztonsági szolgáltatások hibrid biztonsági mentésekhez, amely használja az Azure Backup védelme érdekében
description: Biztonsági funkciók használata az Azure Backup biztonságosabb a biztonsági másolatok készítése
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: trinadhk
ms.openlocfilehash: fcb5941c56eda19f9c524a2c078a76483426b862
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266994"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Biztonsági szolgáltatások hibrid biztonsági mentésekhez, amely használja az Azure Backup védelme érdekében
Növeli a biztonsági problémákat, például a kártevők, a zsarolóprogramok és a behatolás, kapcsolatos elvárásainak. Lehet, hogy a biztonsági kérdésekkel költséges, pénzt és a adatok tekintetében. Az ilyen támadások ellen védő, az Azure Backup mostantól biztosít biztonsági szolgáltatások hibrid biztonsági mentésekhez védelme érdekében. Ez a cikk bemutatja, hogyan engedélyezheti és használja ezeket a funkciókat az Azure Recovery Services agent és az Azure Backup Server használatával. Ezek a funkciók a következők:

- **Megelőzési**. Egy további hitelesítési réteg jelenik meg, amikor például a jelszó módosítása egy kritikus fontosságú műveletet hajtja végre. Ellenőrzés, hogy győződjön meg arról, hogy ezeket a műveleteket végezhet el csak érvényes Azure hitelesítő adatokkal rendelkező felhasználók számára.
- **Riasztási**. E-mail-értesítés érkezik az előfizetés rendszergazdája, amikor például a biztonsági mentési adatok törlése a kritikus fontosságú műveletet hajtja végre. Ez az e-mail biztosítja, hogy a felhasználó értesítést kap gyorsan az ilyen műveletekkel kapcsolatos.
- **Helyreállítási**. Törölt biztonsági mentési adatokat a törlési napjától számított 14 napig őrizzük meg egy további. Ez biztosítja az adatok helyreállíthatóságának egy adott időtartamon belül, így nincs adatvesztés nélküli, még akkor is, ha a támadás akkor fordul elő. Emellett minimális helyreállítási pontok nagyobb számú sérült adatok ellen védő karbantartása.

> [!NOTE]
> Biztonsági funkciók használata infrastruktúra-szolgáltatás (IaaS) virtuális gépek biztonsági mentését, nem szabad engedélyezni. Ezek a funkciók még nem érhető el az IaaS virtuális gépek biztonsági mentését, így lehetővé téve nem kell semmilyen hatással. Biztonsági szolgáltatások csak akkor, ha használ engedélyezni kell: <br/>
>  * **Az Azure Backup ügynöke**. Legalább az ügynök 2.0.9052-es verzióját. Engedélyeznie kell ezeket a funkciókat, frissítenie kell az ügynök verzióra kritikus műveletek végrehajtásához. <br/>
>  * **Az Azure Backup Server**. Minimális Azure Backup ügynök verziója 2.0.9052 az Azure Backup Server 1. frissítés. <br/>
>  * **A System Center Data Protection Manager**. Minimális Azure Backup ügynök 2.0.9052-es verzióját a Data Protection Manager 2012 R2 UR12 vagy a Data Protection Manager 2016 ur2-t. <br/>


> [!NOTE]
> Ezek a szolgáltatások csak a Recovery Services-tároló érhetők el. Minden újonnan létrehozott Recovery Services-tároló ezeket a funkciókat, alapértelmezés szerint engedélyezve van. Meglévő Recovery Services-tárolók a felhasználók a funkciók engedélyezéséhez az alábbi szakaszban ismertetett lépések segítségével. Miután a szolgáltatások engedélyezve vannak, vonatkoznak azokra a Recovery Services agent számítógépeit, az Azure Backup Server-példányokat, és a Data Protection Manager-kiszolgáló regisztrálva a tároló. A beállítás engedélyezése egy egyszeri művelet, és azokat engedélyezése után ezeket a funkciókat nem tiltható le.
>

## <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése
Recovery Services-tároló létrehozásakor használhatja a biztonsági funkciók. Ha egy meglévő tároló dolgozik, a biztonsági funkciók engedélyezéséhez az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra Azure hitelesítő adatait.
2. Válassza ki **Tallózás**, és írja be **Recovery Services**.

    ![Képernyőkép az Azure portál lehetőség](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    A Recovery Services-tárolók listája megjelenik. Ebből a listából válasszon ki egy tárolót. Megnyílik a kiválasztott tároló irányítópultja.
3. Elemek listájában, amely alatt megjelenik a tároló **beállítások**, kattintson a **tulajdonságok**.

    ![Képernyőkép – a Recovery Services tároló beállításai](./media/backup-azure-security-feature/vault-list-properties.png)
4. A **biztonsági beállítások**, kattintson a **frissítés**.

    ![Képernyőkép – a Recovery Services-tároló tulajdonságai](./media/backup-azure-security-feature/security-settings-update.png)

    A frissítés hivatkozás megnyitja a **biztonsági beállítások** panel, amely a szolgáltatások összegzését tartalmazza, és lehetővé teszi engedélyezheti őket.
5. A legördülő listából **konfigurálta az Azure multi-factor Authentication?**, válasszon ki egy értéket, győződjön meg arról, ha engedélyezte a [Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md). Ha engedélyezve van, a rendszer megkéri másik eszközről (például mobiltelefon) történő hitelesítéséhez az Azure Portalra való bejelentkezés során.

   Biztonsági mentés kulcsfontosságú műveleteket hajt végre, ha akkor adja meg a biztonsági PIN-kód, az Azure Portalon elérhető. Azure multi-factor Authentication biztonsági réteget ad hozzá. Csak a feljogosított felhasználók érvényes Azure hitelesítő adataival, és hitelesítést hajtottak végre egy másik eszközön, az Azure Portalon érheti el.
6. Biztonsági beállítások mentéséhez, majd válassza **engedélyezése** kattintson **mentése**. Választhat **engedélyezése** csak egy értéket kiválasztása után a **konfigurálta az Azure multi-factor Authentication?** lista az előző lépésben.

    ![Képernyőkép a biztonsági beállítások](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>A törölt biztonsági mentési adatok helyreállítása
Biztonsági másolat egy további 14 napig őrzi meg a törölt biztonsági mentési adatokat, és nem törli azonnal, ha a **biztonsági mentési adatok törlésével a biztonsági mentés leállítása** műveletet hajtja végre. Ezek az adatok visszaállításához a 14 napos időszakban, a következő lépésekkel, attól függően, amit használ:

A **Azure Recovery Services agent** felhasználók:

1. Ha a számítógépen, ahol is történik a biztonsági mentések továbbra is elérhető, a törölt adatforrások védelmének újbóli beállításához, és használja a [helyreállíthatja azokat ugyanarra a gépre](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) az Azure Recovery Services, a régi helyreállítási pontokat.
2. Ha a számítógép nem érhető el, használja a [helyreállítás egy másik számítógépre](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) egy másik Azure Recovery Services-számítógép segítségével az adatok.

A **az Azure Backup Server** felhasználók:

1. Ha a kiszolgáló, ahol is történik a biztonsági mentések továbbra is elérhető, a törölt adatforrások védelmének újbóli beállításához, és használja a **adatok helyreállítása** funkció, amellyel a régi helyreállítási pontokat.
2. Ha a kiszolgáló nem érhető el, használja a [adatok helyreállítása egy másik Azure Backup Server](backup-azure-alternate-dpm-server.md) egy másik Azure Backup Server-példány használatával az adatok.

A **Data Protection Manager** felhasználók:

1. Ha a kiszolgáló, ahol is történik a biztonsági mentések továbbra is elérhető, a törölt adatforrások védelmének újbóli beállításához, és használja a **adatok helyreállítása** funkció, amellyel a régi helyreállítási pontokat.
2. Ha a kiszolgáló nem érhető el, használja a [külső DPM hozzáadása](backup-azure-alternate-dpm-server.md) az adatok egy másik Data Protection Manager-kiszolgáló használatára.

## <a name="prevent-attacks"></a>Támadások megelőzése érdekében
Ellenőrzi, hogy csak akkor érvényes, ha a felhasználók különféle műveleteket hajthat végre lettek hozzáadva. Ezek közé tartozik egy további hitelesítési réteg hozzáadása és helyreállítási célból a minimális megőrzési karbantartása.

### <a name="authentication-to-perform-critical-operations"></a>Hitelesítés kritikus műveletek végrehajtása
Hajt végre egy biztonsági PIN-kód megadását kéri hozzáadása egy plusz hitelesítési réteggel, a kritikus műveletekre vonatkozó részeként **védelem leállítása az adatok törlésével** és **módosítása jelszót** műveletek.

> [!NOTE]

> Jelenleg biztonsági PIN-kódot nem támogatott a **védelem leállítása az adatok törlésével** DPM és a MABS.

A PIN-kód fogadásához:

1. Jelentkezzen be az Azure portálra.
2. Keresse meg a **Recovery Services-tároló** > **beállítások** > **tulajdonságok**.
3. A **biztonsági PIN-kódot**, kattintson a **Generate**. Ekkor megnyílik egy panel, amely tartalmazza az Azure Recovery Services agent felhasználói felületén megadni a PIN-kódot.
    A PIN-kód érvényes, csak öt perc alatt, és lekérdezi az időszak elteltével automatikusan létrehozott.

### <a name="maintain-a-minimum-retention-range"></a>A minimális adatmegőrzési tartomány karbantartása
Győződjön meg róla, hogy nincsenek mindig egy érvényes helyreállítási pontok száma érhető el, a következő ellenőrzések érhetők el:

- A napi megőrzése beállításnál legalább **hét** napos adatmegőrzést kell elvégezni.
- A heti megőrzése beállításnál legalább **négy** hetes megőrzési kell elvégezni.
- A havi megőrzése beállításnál legalább **három** hónapos megőrzéssel kell elvégezni.
- Az éves megőrzési, legalább **egy** éves megőrzési kell elvégezni.

## <a name="notifications-for-critical-operations"></a>A kritikus műveletekre vonatkozó értesítések
Általában egy kritikus fontosságú műveletet hajtja végre, ha az előfizetés rendszergazdája zajlik a művelet részleteit tartalmazó e-mail értesítés. Ezek az értesítések e-mail további címzettjei konfigurálhatja az Azure portal használatával.

A cikkben említett biztonsági funkciók adja meg a célzott támadások elleni védelmi mechanizmus. Ami még fontosabb a támadás akkor fordul elő, ha ezek a funkciók lehetővé teszik, az adatok helyreállításához.

## <a name="troubleshooting-errors"></a>Hibaelhárítás
| Művelet | A hiba részletei | Megoldás: |
| --- | --- | --- |
| Szabályzat módosítása |Nem sikerült módosítani a biztonsági mentési szabályzat. Hiba: Az aktuális művelet belső szolgáltatáshiba [0x29834] miatt nem sikerült. Várjon egy kis ideig, majd ismételje meg a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |**OK:**<br/>Ez a hiba érhető el, ha biztonsági beállításai engedélyezve vannak, próbálja meg csökkenteni a megőrzési időtartam alatt a fent meghatározott minimális értékeket, és a verzió nem támogatott (az első jegyezze fel, hogy ez a cikk a támogatott verziók vannak megadva). <br/>**Javasolt művelet:**<br/> Ebben az esetben kell beállítania a megőrzési időszak fent a megadott időszak minimális megőrzési (négy hetes, naponta, hetente, havonta vagy egy év az éves ütemezéshez három héttel a hét nap) házirend folytatásához kapcsolatos frissítések. Szükség esetén legmegfelelőbb módszer lehet frissíteni a Backup szolgáltatás ügynöke, az Azure Backup Server és/vagy a DPM UR kihasználhatja a biztonsági frissítéseket. |
| Jelszó módosítása |Biztonsági megadott PIN-kód helytelen. (AZONOSÍTÓ: 100130) adja meg a helyes biztonsági PIN-kódot a művelet végrehajtásához. |**OK:**<br/> Ezt a hibát tartalmaz (ilyen például a jelszó módosítása) kritikus művelet végrehajtása közben érvénytelen vagy lejárt biztonsági PIN-kód megadásakor. <br/>**Javasolt művelet:**<br/> A művelet végrehajtásához meg kell adnia az érvényes biztonsági PIN-kódot. A PIN-kód lekéréséhez jelentkezzen be az Azure Portalra, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód használatával módosítsa a jelszót. |
| Jelszó módosítása |Sikertelen volt a művelet. AZONOSÍTÓ: 120002 |**OK:**<br/>Ez a hiba érhető el, ha biztonsági beállításai engedélyezve vannak, megpróbálja módosítani a jelszót, és a verzió nem támogatott (Ez a cikk először jegyezze fel a megadott érvényes verzió).<br/>**Javasolt művelet:**<br/> Jelszó módosításához először frissítenie kell biztonságimásolat-készítő ügynök a minimális 2.0.9052-es minimális, minimális Update 1, az Azure Backup server és/vagy a DPM minimális a DPM 2012 R2 UR12 vagy a DPM 2016 UR2 (letöltési hivatkozásokat az alábbi), majd adjon meg érvényes biztonsági PIN-kódot. A PIN-kód lekéréséhez jelentkezzen be az Azure Portalra, és navigáljon a Recovery Services-tároló > Beállítások > Tulajdonságok > biztonsági PIN-kód készítése. A PIN-kód használatával módosítsa a jelszót. |

## <a name="next-steps"></a>További lépések
* [Ismerkedés az Azure Recovery Services-tároló](backup-azure-vms-first-look-arm.md) ahhoz, hogy ezeket a funkciókat.
* [Töltse le a legújabb Azure Recovery Services-ügynök](https://aka.ms/azurebackup_agent) Windows-számítógépek védelméhez és a biztonsági mentési adatok támadások elleni védelme érdekében.
* [Töltse le a legújabb Azure Backup Server](https://aka.ms/latest_azurebackupserver) számítási feladatok védelmét, és a biztonsági mentési adatok támadások elleni védelme érdekében.
* [Töltse le a System Center 2012 R2 Data Protection Manager UR12](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) vagy [töltse le a System Center 2016 Data Protection Manager UR2](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) számítási feladatok védelmét, és a biztonsági mentési adatok támadások elleni védelme érdekében.
