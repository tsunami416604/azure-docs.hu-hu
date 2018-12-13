---
title: A helyszíni SQL Server – az Azure Machine Learning Studióban |} A Microsoft Docs
description: Az Azure Machine Learning Studio fejlett elemzéseket végezzenek egy helyszíni SQL Server-adatbázis adatait használják.
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.openlocfilehash: 059dd3d902409abfa80be2b7aac579a54de1868f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090670"
---
# <a name="perform-analytics-with-azure-machine-learning-using-an-on-premises-sql-server-database"></a>Hajtsa végre az analytics az Azure Machine Learning használatával egy helyszíni SQL Server-adatbázisból

Gyakran olyan vállalatok, amelyek a helyszíni adatok használata, ha a méretezési csoport és a gépi tanulási célú számítási feladatokhoz a felhő rugalmasságát. De azok nem szeretné, hogy megszakítja az aktuális üzleti és munkafolyamatok által a helyszíni adatok áthelyezése a felhőbe. Az Azure Machine Learning mostantól támogatja az adatok beolvasása helyszíni SQL Server-adatbázisból, és ezután képzés, és ezeket az adatokat a modell pontozása. Már nem kell manuálisan másolja, és szinkronizálja az adatokat a felhőben és a helyszíni kiszolgáló között. Ehelyett a **adatok importálása** modul az Azure Machine Learning Studióban elolvashatja közvetlenül a helyszíni SQL Server-adatbázisát a tanítási és pontozási feladatok.

Ez a cikk áttekintést a bejövő forgalom a helyszíni SQL server-adatok Azure Machine Learning studióba. Feltételezi, hogy ismeri a munkaterületek, a modulok, az adatkészletek, a kísérletek, például az Azure Machine Learning-fogalmak *stb.*.

> [!NOTE]
> Ez a funkció nem érhető el a munkaterületek. További információ a Machine Learning díjszabás és szint: [Azure Machine Learning szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>A Data Factory saját üzemeltetésű integrációs modul telepítése
Az Azure Machine Learning egy helyszíni SQL Server-adatbázis eléréséhez meg kell töltse le és telepítse a Data Factory helyi Integration Runtime, korábbi nevén az adatkezelési átjárót. Amikor a kapcsolat a Machine Learning Studióban konfigurálja, lehetősége van, töltse le és telepítse az Integration Runtime (IR) használatával a **töltse le és register data gatewayt** párbeszédpanel az alábbiakban.


Is telepítheti a kívánt időben integrációs modul letöltésével és futtatásával az MSI-telepítő csomag az a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Az MSI is használható egy már meglévő integrációs modul frissítése a legújabb verzióra, az összes beállítás megőrzi.

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
* Egyszerre csak egy munkaterülethez egy IRs konfigurálja. Integrációs modulok jelenleg nem oszthatók meg munkaterületek között.
* Egyetlen munkaterület több IRs konfigurálhatja. Például előfordulhat, hogy használni kívánt egy integrációs Modult, amely az adatforrásokhoz kapcsolódik, a teszt során fejlesztési és a egy éles integrációs modul Ha készen áll az üzembe helyezés.
* Az integrációs modul nem kell ugyanarra a gépre, az adatforrással kell. De az adatforrás közelebb tartózkodó lerövidíti az átjáró csatlakozik az adatforráshoz. Azt javasoljuk, hogy az integrációs modul telepítését olyan számítógépen, amelyen eltér, amely üzemelteti a helyszíni adatforrás úgy, hogy az erőforrások az átjáró és az adatforrás nem vagyunk versenyképesek.
* Ha már rendelkezik egy Power bi-ban vagy az Azure Data Factory forgatókönyvek szolgáló számítógépre telepített integrációs modul, telepítse külön IR az Azure Machine Learning egy másik számítógépen.

  > [!NOTE]
  > Data Factory helyi integrációs modul és a Power BI Gateway ugyanazon a számítógépen nem futtatható.
  >
  >
* Szeretné használni a Data Factory helyi integrációs modul az Azure Machine Learning, még akkor is, ha az Azure ExpressRoute használ az egyéb adatokat. Kezelje az adatforrás egy helyszíni adatforráshoz (Ez egy tűzfal mögé), még akkor is az ExpressRoute használatakor. A Data Factory helyi integrációs modul használatával Machine Learning és az adatforrás közötti kapcsolatot.

A cikk részletes információkat a telepítési előfeltételek, telepítés lépéseit, hibaelhárítási tippekkel szolgál találhatja [a Data Factory saját üzemeltetésű integrációs](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Bejövő adatok a helyszíni SQL Server-adatbázisát az Azure Machine Learningbe
Ez az útmutató egy Azure Data Factory integrációs modul beállítása az Azure Machine Learning-munkaterület, konfigurálása, és ezután olvassa el az adatokat a helyszíni SQL Server-adatbázisból.

> [!TIP]
> A Kezdés előtt tiltsa le a böngésző előugróablak-blokkolóját a `studio.azureml.net`. Ha a Google Chrome böngészőt használ, töltse le és telepítsen több beépülő modulok érhető el a Google Chrome Webáruház [kattintson egyszer bővítmény](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Az Azure Data Factory integrációs modulnak volt nevén adatkezelési átjáró. A részletes oktatóanyag továbbra is átjáróként hivatkozik rá.  

### <a name="step-1-create-a-gateway"></a>1. lépés: Az átjáró létrehozása
Az első lépéseként hozhat létre, és állítsa be az átjáró a helyszíni SQL-adatbázis eléréséhez.

1. Jelentkezzen be [Azure Machine Learning Studio](https://studio.azureml.net/Home/) , és válassza ki használni kívánt munkaterületet.
2. Kattintson a **beállítások** a bal oldali panelen, és kattintson a **DATA GATEWAYS** a felső fülön.
3. Kattintson a **új DATA GATEWAYRE** a képernyő alján.

    ![Új átjáró](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Az a **új data gatewayre** párbeszédpanelen adja meg a **átjárónév** , és ha szükséges, hozzáadhat egy **leírás**. Kattintson a konfiguráció a következő lépéssel, jobb alsó sarokban lévő nyílra.

    ![Adja meg az átjáró nevét és leírását](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Töltse le és register data átjáró párbeszédpanelen az ÁTJÁRÓ SZOLGÁLTATÁSREGISZTRÁCIÓS kulcs másolása a vágólapra.

    ![Töltse le és regisztrálása az adatátjáróval](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Ha még nincs letöltötte és telepítette a Microsoft adatkezelési átjáró, majd kattintson **letöltési adatkezelési átjáró**. Ekkor megjelenik a Microsoft Download Centert, amelyen van szüksége, töltse le az átjáró-verziónak a kiválasztása, és telepítse azt. Annak részletes információt a telepítési előfeltételek, telepítés lépéseit, hibaelhárítási tippekkel szolgál az a cikk elején szakaszok [adatok áthelyezése a felhőbe az adatkezelési átjáróval és a egy helyszíni forrásra](../../data-factory/tutorial-hybrid-copy-portal.md) .
7. Az átjáró telepítése után a Data Management Gateway Configuration Manager megnyílik, és a **Register átjáró** párbeszédpanel jelenik meg. Illessze be a **átjáró Szolgáltatásregisztrációs kulcs** , hogy másolja a vágólapra, majd kattintson a **regisztrálása**.
8. Ha már rendelkezik egy átjáró telepített, futtassa a Data Management Gateway Configuration Manager. Kattintson a **módosítókulcs**, illessze be a **átjáró Szolgáltatásregisztrációs kulcs** , az előző lépésben a vágólapra, majd kattintson az **OK**.
9. Ha a telepítés befejeződött, a **Register átjáró** for Microsoft Data Management Gateway Configuration Manager párbeszédpanel jelenik meg. Illessze be az előző lépésben a vágólapra kimásolt ÁTJÁRÓ regisztrációs kulcsot, és kattintson a **regisztrálása**.

    ![Átjáró regisztrálása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Az átjáró konfigurációja befejeződött, a következő értékeket a beállításakor a **kezdőlap** lapon a Microsoft Data Management Gateway Configuration Managerben:

    * **Átjáró neve** és **példánynév** vannak beállítva az átjárója nevére.
    * **Regisztrációs** értékre van állítva **regisztrált**.
    * **Állapot** értékre van állítva **elindítva**.
    * A lent láthatók az állapotsorban **Data Management Gateway Felhőszolgáltatás csatlakoztatott** mellett egy zöld pipa.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Az Azure Machine Learning Studio is frissül a Ha a regisztráció sikeres volt.

    ![Átjáró regisztrálása sikeres](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Az a **töltse le és regisztrálása az adatátjáróval** párbeszédpanelen kattintson a pipa jelre a telepítés befejezéséhez. A **beállítások** lapon megjelenik az "Elérhető" az átjáró állapotának ellenőrzéséhez. A jobb oldali ablaktáblán állapota és egyéb hasznos információkat találhat.

    ![Átjáró beállításai](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Váltás a Microsoft Data Management Gateway Configuration Manager a **tanúsítvány** fülre. Ezen a lapon megadott tanúsítvány segítségével a helyszíni adattár, amely a portálon megadott hitelesítő adatok titkosítása/visszafejtése. Ez a tanúsítvány az alapértelmezett tanúsítvány. A Microsoft javasolja, hogy ez a tanúsítvány felügyeleti rendszer biztonsági mentése a saját tanúsítványban módosításával. Kattintson a **módosítása** inkább saját tanúsítvány használata.

    ![Átjáró-tanúsítvány módosítása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (nem kötelező) Ha azt szeretné, hogy hibaelhárítási részletes naplózás engedélyezése az átjáró, a Microsoft Data Management Gateway Configuration Manager problémái váltson a **diagnosztikai** fülre és ellenőrizze a **részletes engedélyezése hibaelhárítás céljából naplózás** lehetőséget. A naplózási információk mellett a Windows eseménynaplójában található a **alkalmazások és szolgáltatásnaplók**  - &gt; **adatkezelési átjáró** csomópont. Is használhatja a **diagnosztikai** fülre, és tesztelje a kapcsolatot egy helyszíni adatforráshoz az átjárót.

    ![Részletes naplózás engedélyezése](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Ezzel befejezte az Azure Machine Learning, az átjáró telepítési folyamat.
Most már készen áll a helyszíni adatok használatát.

Hozzon létre, és állítsa be a Studióban minden munkaterülethez több átjárót. Például előfordulhat, hogy szeretne csatlakozni a fejlesztés során, a teszt adatforrások átjáró és a egy másik átjáró a termelési adatforrások. Az Azure Machine Learning lehetővé teszi több átjáró attól függően, hogy a vállalati környezet beállításához. Jelenleg nem oszthat meg egy átjárót a munkaterületek között, és csak egy átjáró telepíthető ugyanazon a számítógépen. További információkért lásd: [adatok áthelyezése a felhőbe az adatkezelési átjáróval és a egy helyszíni forrásra](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2. lépés: Az átjáró használatára, egy helyszíni adatforrásból származó adatokat olvasni.
Az átjáró beállítása után hozzáadhat egy **adatok importálása** modult egy, amely bemenetként az adatokat a helyszíni SQL Server-adatbázisból.

1. A Machine Learning Studióban, válassza ki a **kísérletek** lapra, majd **+ új** a bal alsó sarokban, és válassza a **üres kísérlet** (vagy válassza ki az egyik több minta kísérletek érhető el).
2. Keresse meg és húzza a **adatok importálása** modult a kísérletvászonra.
3. Kattintson a **Mentés másként** a vászon alatti. Adja meg az "Azure Machine Learning a helyszíni SQL Server-oktatóanyag" kísérlet nevét, válassza ki a munkaterületet, majd kattintson a **OK** pipa jelre.

   ![Kísérlet mentése új néven](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kattintson a **adatok importálása** modult, válassza ki, majd a **tulajdonságok** panelen a vászontól jobbra a "Helyi SQL Database" Válassza ki a **adatforrás** legördülő listában.
5. Válassza ki a **adatátjáró** telepítve, és regisztrálva. Válassza a "(új Data átjáró hozzáadása...)" állíthat be egy másik átjárót.

   ![Adatok importálása modullal data gateway kiválasztása](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Adja meg az SQL **adatbázis-kiszolgáló neve** és **adatbázisnév**, és az SQL **adatbázis-lekérdezés** kíván végrehajtatni.
7. Kattintson a **adja meg az értékeket** alatt **felhasználónevet és jelszót** adja meg az adatbázis hitelesítő adatait. Használhatja az integrált Windows-hitelesítést vagy SQL Server-hitelesítéssel attól függően, hogy hogyan a helyszíni SQL Server konfigurálva van-e.

   ![Adja meg az adatbázis-hitelesítő adatok](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   A "értékek set", egy zöld pipának üzenet "értékeket a kötelező" megváltozik. Csak meg kell a hitelesítő adatok egyszer, kivéve, ha az adatbázis-információ vagy jelszavának megváltozása után. Az Azure Machine Learning a felhőben a hitelesítő adatok titkosításához az átjáró telepítésekor megadott tanúsítványt használja. Az Azure soha nem tárolja a helyszíni hitelesítő adatok titkosítás nélkül.

   ![Adattulajdonságok modul importálása](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kattintson a **futtatása** a kísérlet futtatásához.

A kísérlet a lejáratot követően újrainduljon, ha a kimeneti portjára, kattintson az adatbázisból importált adatokat jelenítheti meg a **adatok importálása** modul és kiválasztásával **Visualize**.

Ha befejezte a kísérletét fejlesztése, üzembe helyezése, és a modell üzembe helyezése. A kötegelt végrehajtási szolgáltatás, a helyszíni SQL Server adatainak használatával adatbázis konfigurált a **adatok importálása** modul lesznek olvashatók és pontozás használt. Használhatja a kérés-válasz szolgáltatás pontozási a helyszíni adatokat, amíg a Microsoft használatát javasolja a [Excel-bővítményben](excel-add-in-for-web-services.md) helyette. Jelenleg egy helyszíni SQL Server-adatbázis – írás **adatok exportálása** nem támogatott, vagy a kísérleti vagy közzétett webszolgáltatásokat.
