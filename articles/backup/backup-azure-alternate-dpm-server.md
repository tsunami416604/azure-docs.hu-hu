---
title: Adatok helyreállítása Azure biztonsági másolat készítő kiszolgálóról
description: A recovery services-tárolóban védett adatok helyreállítása az adott tárolóba regisztrált bármely Azure Backup Server kiszolgálóról.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173504"
---
# <a name="recover-data-from-azure-backup-server"></a>Adatok helyreállítása az Azure Backup Serverről

Az Azure Backup Server segítségével helyreállíthatja a helyreállítási szolgáltatások tárolóba biztonsági mentésre használt adatokat. Az ehhez szükséges folyamat integrálva van az Azure Backup Server felügyeleti konzoljába, és hasonló a többi Azure Backup-összetevő helyreállítási munkafolyamatához.

> [!NOTE]
> Ez a cikk a [System Center Data Protection Manager 2012 R2 rendszerre vonatkozik, amely nek 7 vagy újabb ur7-es vagy újabb,](https://support.microsoft.com/kb/3065246)és a legújabb Azure [Backup-ügynökkel](https://aka.ms/azurebackup_agent)együtt kell fizetnie.
>
>

Adatok helyreállítása Azure backup kiszolgálóról:

1. Az Azure Backup Server felügyeleti konzol **helyreállítási** lapján kattintson a **"Külső DPM hozzáadása"** gombra (a képernyő bal felső részén).

    ![Külső DPM hozzáadása](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Töltse le az új **tároló hitelesítő adatait** az Azure Backup **Serverhez** társított tárolóból, ahol az adatok helyreállítása folyamatban van, válassza ki az Azure Backup Servert a Recovery Services-tárolóban regisztrált Azure biztonsági mentési kiszolgálók listájából, és adja meg a helyreállított kiszolgálóhoz társított **titkosítási jelszót.**

    ![Külső DPM-hitelesítő adatok](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Csak az ugyanahhoz a regisztrációs tárolóhoz társított Azure backup-kiszolgálók állíthatják helyre egymás adatait.
   >
   >

    A külső Azure biztonsági mentési kiszolgáló sikeres hozzáadása után a helyreállítási **lapon** tallózhat a külső kiszolgáló és a helyi Azure backup-kiszolgáló adatai között.
3. Böngésszen a rendelkezésre álló termelési kiszolgálók rendelkezésre álló, a külső Azure Backup Server által védett, és válassza ki a megfelelő adatforrást.

    ![Tallózás külső DPM-kiszolgálóközött](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Válassza ki **a hónapot és** az **évet** a Helyreállítási pontok legördülő menüből, válassza ki a helyreállítási pont létrehozásának szükséges **helyreállítási dátumát,** és válassza ki a **Helyreállítási időt**.

    A fájlok és mappák listája megjelenik az alsó ablaktáblán, amely bármely helyen böngészhető és visszaadható.

    ![Külső DPM-kiszolgáló helyreállítási pontjai](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kattintson a jobb gombbal a megfelelő elemre, és válassza a **Helyreállítás (Helyreállítás)** elemet.

    ![Külső DPM-helyreállítás](./media/backup-azure-alternate-dpm-server/recover.png)
6. Tekintse át a **Kijelölés helyreállítása című elemet.** Ellenőrizze a biztonsági másolat adatait és idejét, valamint azt a forrást, amelyből a biztonsági másolat készült. Ha a kijelölés helytelen, kattintson a **Mégse gombra** a helyreállítási lapra való visszanavigáláshoz a megfelelő helyreállítási pont kiválasztásához. Ha a kijelölés helyes, kattintson a **Tovább**gombra.

    ![Külső DPM-helyreállítási összefoglaló](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Válassza **a Helyreállítás másik helyre**lehetőséget. **Tallózással** keresse meg a megfelelő helyet a helyreállításhoz.

    ![Külső DPM helyreállítási alternatív hely](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Válassza a másolás , **Kihagyás**vagy **Felülírás** **létrehozásához**kapcsolódó beállítást.

   * **Másolat létrehozása** – névütközés esetén másolatot hoz létre a fájlról.
   * **Skip** - ha van egy név ütközés, nem állítja vissza a fájlt, amely elhagyja az eredeti fájlt.
   * **Felülírja** - ha névütközés van, felülírja a fájl meglévő példányát.

     Válassza a **biztonság visszaállításához**szükséges megfelelő beállítást. Alkalmazhatja annak a célszámítógépnek a biztonsági beállításait, ahol az adatok helyreállítása történik, vagy azokat a biztonsági beállításokat, amelyek a helyreállítási pont létrehozásakor a termékre vonatkoztak.

     Azonosítsa, hogy a rendszer küldjön-e **értesítést,** miután a helyreállítás sikeresen befejeződött.

     ![Külső DPM helyreállítási értesítések](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Az **Összefoglaló** képernyő felsorolja az eddig kiválasztott lehetőségeket. Miután a **"Helyreállítás"** gombra kattintott, az adatok a megfelelő helyszíni helyre kerül.

    ![Külső DPM helyreállítási beállítások összegzése](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A helyreállítási feladat figyelhető az Azure Backup Server **figyelése** lapon.
   >
   >

    ![A helyreállítás figyelése](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. A külső **DPM-kiszolgáló** nézetének eltávolításához kattintson a Külső DPM törlése gombra a DPM-kiszolgáló **Helyreállítás** lapján.

    ![Külső DPM törlése](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Hibaüzenetek elhárítása

| Nem. | Hibaüzenet | Hibaelhárítási lépések |
|:---:|:--- |:--- |
| 1. |Ez a kiszolgáló nincs regisztrálva a tároló hitelesítő adatai által megadott tárolóban. |**Ok:** Ez a hiba akkor jelenik meg, ha a kiválasztott tároló hitelesítő adatfájl nem tartozik az Azure Backup Serverhez társított Helyreállítási szolgáltatások tárolóhoz, amelyen a helyreállítást megkísérli. <br> **Felbontás:** Töltse le a tároló hitelesítő fájlját a Recovery Services-tárolóból, amelyhez az Azure Backup Server regisztrálva van. |
| 2. |A helyreállítható adatok nem érhetők el, vagy a kijelölt kiszolgáló nem DPM-kiszolgáló. |**Ok:** Nincs más Azure backup kiszolgálók regisztrálva a Recovery Services tároló, vagy a kiszolgálók még nem töltötte fel a metaadatokat, vagy a kiválasztott kiszolgáló nem egy Azure Backup Server (Windows Server vagy Windows ügyfél). <br> **Felbontás:** Ha vannak más Azure backup-kiszolgálók regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van. <br>Ha vannak más Azure backup kiszolgálók regisztrálva a Recovery Services-tároló, várjon egy nappal a telepítés után a helyreállítási folyamat elindításához. Az éjszakai feladat feltölti az összes védett biztonsági mentés metaadatait a felhőbe. Az adatok elérhetők lesznek a helyreállításhoz. |
| 3. |Nincs regisztrálva más DPM-kiszolgáló a tárolóban. |**Ok:** Nincs más Azure biztonsági mentési kiszolgálók, amelyek regisztrálva vannak a tároló, ahonnan a helyreállítás tanait próbálják.<br>**Felbontás:** Ha vannak más Azure backup-kiszolgálók regisztrálva a Recovery Services-tároló, győződjön meg arról, hogy a legújabb Azure Backup-ügynök telepítve van.<br>Ha vannak más Azure backup kiszolgálók regisztrálva a Recovery Services-tároló, várjon egy nappal a telepítés után a helyreállítási folyamat elindításához. Az éjszakai feladat feltölti az összes védett biztonsági mentés metaadatait a felhőbe. Az adatok elérhetők lesznek a helyreállításhoz. |
| 4. |A megadott titkosítási jelszó nem egyezik meg a következő kiszolgálóhoz társított jelszóval: ** \<a kiszolgáló neve>** |**Ok:** Az Azure Backup Server által az Azure Backup Server által visszanyert adatokból származó adatok titkosítása során használt titkosítási jelszó nem egyezik meg a megadott titkosítási jelszóval. Az ügynök nem tudja visszafejteni az adatokat. Ezért a helyreállítás sikertelen.<br>**Felbontás:** Adja meg pontosan ugyanazt a titkosítási jelszót társított Azure Backup Server, amelynek adatait a rendszer helyreáll. |

## <a name="next-steps"></a>További lépések

További gyakori kérdéseket is áttekinthet:

* [Gyakori kérdések](backup-azure-vm-backup-faq.md) az Azure virtuális gépek biztonsági másolataival kapcsolatban
* [Gyakori kérdések](backup-azure-file-folder-backup-faq.md) az Azure Backup-ügynökről
