---
title: On-premises SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Helyszíni SQL Server-adatbázisból származó adatok használatával speciális elemzéseket végezhet Azure Machine Learning Studio (klasszikus).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9afac1adef801956f176dd339c795e2df533a2c7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169119"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Elemzések elvégzése Azure Machine Learning Studio (klasszikus) használatával helyszíni SQL Server-adatbázis segítségével

Gyakran olyan vállalatok, amelyek a helyszíni adatok használata, ha a méretezési csoport és a gépi tanulási célú számítási feladatokhoz a felhő rugalmasságát. De azok nem szeretné, hogy megszakítja az aktuális üzleti és munkafolyamatok által a helyszíni adatok áthelyezése a felhőbe. Azure Machine Learning Studio (klasszikus) mostantól támogatja az adatok helyszíni SQL Server-adatbázisból való beolvasását, majd az adatokkal rendelkező modellek betanítását és pontozását. Már nem kell manuálisan másolja, és szinkronizálja az adatokat a felhőben és a helyszíni kiszolgáló között. Ehelyett a Azure Machine Learning Studio (klasszikus) **adatimportálási** modulja most már közvetlenül a helyszíni SQL Server-adatbázisból is beolvasható a betanítási és pontozási feladatokhoz.

Ez a cikk áttekintést nyújt a helyszíni SQL Server-információk Azure Machine Learning Studio (klasszikus) rendszerbe való beáramlásáról. Feltételezi, hogy már ismeri a Studio (klasszikus) fogalmakat, például a munkaterületeket, modulokat, adatkészleteket, kísérleteket *stb*.

> [!NOTE]
> Ez a funkció nem érhető el a munkaterületek. További információ a Machine Learning díjszabásáról és szintjeiről: [Azure Machine learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>A Data Factory saját üzemeltetésű integrációs modul telepítése
Ha Azure Machine Learning Studio (klasszikus) helyszíni SQL Server-adatbázist szeretne elérni, le kell töltenie és telepítenie kell a Data Factory saját üzemeltetésű Integration Runtime, korábbi nevén adatkezelés átjáróként. Ha a Machine Learning Studio (klasszikus) szolgáltatásban konfigurálja a kapcsolatot, lehetősége van a Integration Runtime (IR) letöltésére és telepítésére az alább ismertetett **letöltés és regisztrálás adatátjáró** használatával.


Az IR-t az idő előtt is telepítheti, ha letölti és futtatja az MSI-telepítőcsomagot a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717). Az MSI-t a meglévő IR legújabb verzióra történő frissítésére is használhatja, minden beállítás megőrzött.

A Data Factory integrációs modulnak előfeltételei a következők:

* A Data Factory helyi integráció csak a .NET-keretrendszer 4.6.1-es verzióját vagy újabb 64 bites operációs rendszer.
* A Windows operációs rendszerek támogatott verzióinak a Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 rendszer. 
* Az ajánlott konfiguráció az integrációs modul gép legalább 2 GHz-es, 4 mag CPU, 8GB RAM-MAL és 80GB lemezterület.
* Ha a gazdagép frissítéséből hibernálás az integrációs modul kérelmek nem fog válaszolni. Ezért egy megfelelő energiasémát beállítani a számítógépen bemutathatja telepítése előtt Ha a számítógép hibernált állapotba van konfigurálva, az integrációs modul telepítése üzenetet jelenít meg.
* A másolási tevékenység egy adott gyakorisággal következik be, mert az erőforrás-használat (CPU, memória) a gépen is ugyanazt a mintát követi, csúcs-és üresjárati. Erőforrás-használat is nagyban függ az áthelyezett adatok mennyisége. Több másolási feladat van folyamatban, meg fogjuk megfigyelheti csúcsidőben feljebb görgetünk erőforrás-használat. Bár technikailag elegendő a fent felsorolt minimális konfiguráció, érdemes több erőforrást, mint a minimális konfigurációs függően az adatok áthelyezését a meghatározott típusú konfigurációt.

Vegye figyelembe a következőket, amikor beállításával és használatával a Data Factory helyi Integration Runtime:

* Integrációs modul csak egyetlen példányát telepítheti egyetlen számítógépre.
* Több helyszíni adatforrás olyan egységes integrációs modul is használhat.
* Több IRs más-más számítógépekre csatlakozhat a helyszíni ugyanazon az adatforráson.
* Egyszerre csak egy munkaterülethez konfigurálhat egy IRs-t. Integrációs modulok jelenleg nem oszthatók meg munkaterületek között.
* Egyetlen munkaterület több IRs konfigurálhatja. Előfordulhat például, hogy egy olyan IR-t szeretne használni, amely a tesztelési adatforrásokhoz van csatlakoztatva a fejlesztés során, valamint egy éles IR-t, amikor készen áll a működővé tenni.
* Az integrációs modul nem kell ugyanarra a gépre, az adatforrással kell. De az adatforrás közelebb tartózkodó lerövidíti az átjáró csatlakozik az adatforráshoz. Azt javasoljuk, hogy az integrációs modul telepítését olyan számítógépen, amelyen eltér, amely üzemelteti a helyszíni adatforrás úgy, hogy az erőforrások az átjáró és az adatforrás nem vagyunk versenyképesek.
* Ha a számítógépen már telepítve van egy Power BI vagy Azure Data Factory forgatókönyveket kiszolgáló IR, telepítsen egy különálló IR-t a Azure Machine Learning Studio (klasszikus) számára egy másik számítógépen.

  > [!NOTE]
  > Data Factory helyi integrációs modul és a Power BI Gateway ugyanazon a számítógépen nem futtatható.
  >
  >
* A Azure Machine Learning Studio (klasszikus) Data Factory saját üzemeltetésű Integration Runtimet kell használnia, még akkor is, ha más-más adataihoz is használ Azure ExpressRoute. Kezelje az adatforrás egy helyszíni adatforráshoz (Ez egy tűzfal mögé), még akkor is az ExpressRoute használatakor. A Data Factory helyi integrációs modul használatával Machine Learning és az adatforrás közötti kapcsolatot.

A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről a [Data Factory Integration Runtime](../../data-factory/concepts-integration-runtime.md)cikkben talál részletes információt.

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>A helyszíni SQL Server-adatbázisból származó adatok beáramlása Azure Machine Learning
Ebben az útmutatóban egy Azure Data Factory Integration Runtime kell beállítania egy Azure Machine Learning munkaterületen, konfigurálnia kell, majd el kell olvasnia egy helyszíni SQL Server-adatbázis adatait.

> [!TIP]
> Mielőtt elkezdené, tiltsa le a böngésző előugró ablak-blokkoló `studio.azureml.net`ét. Ha a Google Chrome böngészőt használja, töltse le és telepítse a Google Chrome webáruházban elérhető több beépülő modul egyikét, [majd kattintson az alkalmazás kiterjesztésére](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Az Azure Data Factory integrációs modulnak volt nevén adatkezelési átjáró. A részletes oktatóanyag továbbra is átjáróként hivatkozik rá.  

### <a name="step-1-create-a-gateway"></a>1\. lépés: Az átjáró létrehozása
Az első lépéseként hozhat létre, és állítsa be az átjáró a helyszíni SQL-adatbázis eléréséhez.

1. Jelentkezzen be [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net/Home/) webhelyre, és válassza ki azt a munkaterületet, amelyen dolgozni szeretne.
2. Kattintson a bal oldali **Beállítások** panelre, majd kattintson a felül található **adatátjárók** fülre.
3. Kattintson a képernyő alján található **új adatátjáró** elemre.

    ![Új átjáró](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Az **új adatátjáró** párbeszédpanelen adja meg az **átjáró nevét** , és szükség esetén adjon meg egy **leírást**. Kattintson a konfiguráció a következő lépéssel, jobb alsó sarokban lévő nyílra.

    ![Adja meg az átjáró nevét és leírását](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Töltse le és register data átjáró párbeszédpanelen az ÁTJÁRÓ SZOLGÁLTATÁSREGISZTRÁCIÓS kulcs másolása a vágólapra.

    ![Töltse le és regisztrálása az adatátjáróval](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Ha még nem töltötte le és nem telepítette a Microsoft adatkezelés-átjárót, kattintson az **adatkezelési átjáró letöltése**elemre. Ekkor megjelenik a Microsoft Download Centert, amelyen van szüksége, töltse le az átjáró-verziónak a kiválasztása, és telepítse azt. A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről részletes információkat talál az [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáróval](../../data-factory/tutorial-hybrid-copy-portal.md)című cikkben.
7. Az átjáró telepítése után a adatkezelés átjáró Configuration Manager megnyílik, és megjelenik az **átjáró regisztrálása** párbeszédpanel. Illessze be a vágólapra másolt **átjáró-regisztrációs kulcsot** , majd kattintson a **regisztráció**elemre.
8. Ha már rendelkezik egy átjáró telepített, futtassa a Data Management Gateway Configuration Manager. Kattintson a **kulcs módosítása**gombra, illessze be az előző lépésben vágólapra másolt **átjáró regisztrációs kulcsot** , majd kattintson **az OK**gombra.
9. Ha a telepítés befejeződött, megjelenik a Microsoft adatkezelés Gateway-Configuration Manager-átjárójának **regisztrálása** párbeszédpanel. Illessze be az ÁTJÁRÓ regisztrációs KULCSát, amelyet az előző lépésben a vágólapra másolt, majd kattintson a **regisztráció**elemre.

    ![Átjáró regisztrálása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Az átjáró konfigurációja akkor fejeződik be, ha a Microsoft adatkezelés Gateway Configuration Manager **Kezdőlap** lapján a következő értékek vannak beállítva:

    * Az **átjáró** neve és a **példány neve** az átjáró nevére van beállítva.
    * A **regisztráció** a **regisztrált**értékre van állítva.
    * Az **állapot** az **elindítva**értékre van állítva.
    * A Lenti állapotsor megjeleníti a **adatkezelés Gateway Cloud Service-hez csatlakoztatott átjárót** , zöld pipa mellett.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Ha a regisztráció sikeres, a Azure Machine Learning Studio (klasszikus) is frissül.

    ![Átjáró regisztrálása sikeres](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Az **adatátjáró letöltése és regisztrálása** párbeszédpanelen kattintson a pipa jelre a beállítás befejezéséhez. A **Beállítások** lap az átjáró állapotát "online" állapotként jeleníti meg. A jobb oldali ablaktáblán állapota és egyéb hasznos információkat találhat.

    ![Átjáró beállításai](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. A Microsoft adatkezelés átjáró Configuration Manager váltson a **tanúsítvány** lapra. Az ezen a lapon megadott tanúsítvány a portálon megadott helyszíni adattároló hitelesítő adatainak titkosítására és visszafejtésére szolgál. Ez a tanúsítvány az alapértelmezett tanúsítvány. A Microsoft javasolja, hogy ez a tanúsítvány felügyeleti rendszer biztonsági mentése a saját tanúsítványban módosításával. Kattintson a **módosítás** gombra a saját tanúsítványának használatához.

    ![Átjáró-tanúsítvány módosítása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. választható Ha engedélyezni szeretné a részletes naplózást az átjáróval kapcsolatos problémák elhárítása érdekében, a Microsoft adatkezelés átjáró Configuration Manager váltson a **diagnosztika** lapra, és jelölje be a **részletes naplózás engedélyezése hibaelhárítási célokra** beállítást. A naplózási információk a Windows Eseménynapló -&gt; **adatkezelés átjáró** csomópontjának **alkalmazások és szolgáltatások naplói** alatt találhatók. A **diagnosztika** lapon is tesztelheti a helyszíni adatforrással létesített kapcsolatokat az átjáró használatával.

    ![Részletes naplózás engedélyezése](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Ezzel befejezte az átjáró telepítési folyamatát Azure Machine Learning Studio (klasszikus).
Most már készen áll a helyszíni adatok használatát.

Mindegyik munkaterülethez létrehozhat és beállíthat több átjárót a Studio (klasszikus) használatával. Például előfordulhat, hogy szeretne csatlakozni a fejlesztés során, a teszt adatforrások átjáró és a egy másik átjáró a termelési adatforrások. A Azure Machine Learning Studio (klasszikus) lehetőséget biztosít több átjáró beállítására a vállalati környezettől függően. Jelenleg nem oszthat meg egy átjárót a munkaterületek között, és csak egy átjáró telepíthető ugyanazon a számítógépen. További információkért lásd: [adatok áthelyezése a helyszíni források és a felhő között adatkezelés átjáróval](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2\. lépés: Az átjáró használatára, egy helyszíni adatforrásból származó adatokat olvasni.
Az átjáró beállítása után hozzáadhat egy **adatimportálási** modult egy olyan kísérlethez, amely beírja az adatokat a helyszíni SQL Server adatbázisból.

1. A Machine Learning Studio (klasszikus) területen válassza a **kísérletek** fület, kattintson a bal alsó sarokban található **+ új** elemre, majd válassza az **üres kísérlet** lehetőséget (vagy válassza ki az elérhető több minta kísérlet egyikét).
2. Keresse meg és húzza az **adatimportálási** modult a kísérleti vászonra.
3. Kattintson a **Save (Mentés** ) gombra a vászon alatt. Írja be a "Azure Machine Learning Studio (klasszikus) helyszíni SQL Server oktatóanyag" kifejezést a kísérlet neveként, válassza ki a munkaterületet, majd kattintson az **OK** pipa jelre.

   ![Kísérlet mentése új néven](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kattintson az **adatimportálási** modulra a kiválasztásához, majd a vászon jobb oldalán található **Tulajdonságok** ablaktáblán válassza a "helyszíni SQL Database" lehetőséget az **adatforrás** legördülő listájában.
5. Válassza ki a telepített és regisztrált **adatátjárót** . Válassza a "(új Data átjáró hozzáadása...)" állíthat be egy másik átjárót.

   ![Adatok importálása modullal data gateway kiválasztása](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Adja meg az SQL **Database-kiszolgáló nevét** és az **adatbázis nevét**, valamint azt az SQL **Database-lekérdezést** , amelyet végre szeretne hajtani.
7. Kattintson az **értékek megadása** a **Felhasználónév és a jelszó** területen, majd adja meg az adatbázis hitelesítő adatait. Használhatja az integrált Windows-hitelesítést vagy SQL Server-hitelesítéssel attól függően, hogy hogyan a helyszíni SQL Server konfigurálva van-e.

   ![Adja meg az adatbázis-hitelesítő adatok](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   A "értékek set", egy zöld pipának üzenet "értékeket a kötelező" megváltozik. Csak meg kell a hitelesítő adatok egyszer, kivéve, ha az adatbázis-információ vagy jelszavának megváltozása után. A Azure Machine Learning Studio (klasszikus) az átjáró telepítésekor megadott tanúsítványt használja a Felhőbeli hitelesítő adatok titkosításához. Az Azure soha nem tárolja a helyszíni hitelesítő adatok titkosítás nélkül.

   ![Adattulajdonságok modul importálása](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. A kísérlet futtatásához kattintson a **Futtatás** gombra.

Miután a kísérlet futása befejeződött, az **adatok importálása** modul kimeneti portjára kattintva megjelenítheti az adatbázisból importált adatokat, és kiválaszthatja a **Megjelenítés**elemet.

Ha befejezte a kísérletét fejlesztése, üzembe helyezése, és a modell üzembe helyezése. A Batch végrehajtási szolgáltatás használatával az adatok **importálása** modulban konfigurált helyszíni SQL Server adatbázisból származó adatok beolvasása és pontozása történik. Habár a kérés-válasz szolgáltatást használhatja a helyszíni adatkereséshez, a Microsoft az [Excel-bővítmény](excel-add-in-for-web-services.md) használatát javasolja helyette. A helyszíni SQL Server adatbázisra való írás jelenleg nem támogatott a kísérletekben vagy a közzétett webszolgáltatásokban.
