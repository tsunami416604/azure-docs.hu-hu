---
title: "A helyszíni SQL Server használata az Azure gépi tanulás |} Microsoft Docs"
description: "Egy helyi SQL Server adatbázis Azure Machine Learning segítségével speciális elemzés végrehajtásához használható."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: garye;krishnan
ms.openlocfilehash: 79ae5cd78ce07fcc84be49c2693773d58a15771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Bővített analitika az Azure Machine Learning használatával egy helyszíni SQL Server-adatbázisból származó adatokkal

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Gyakran vállalatok számára, amely a helyszíni adatokkal dolgozni szeretne kihasználják a skála és agilitást a gépi tanulási a munkaterheléseket a felhő. De azok nem szeretné, hogy az aktuális üzleti folyamatokat és munkafolyamatok zavarja a helyszíni adatokat át a felhőbe. Az Azure Machine Learning mostantól támogatja az adatok olvasása a helyszíni SQL Server adatbázis, majd képzési és ezeket az adatokat egy modell pontozása. Már nem kell manuálisan másolja, majd az adatok a felhőben és a helyszíni kiszolgáló közötti szinkronizálása. Ehelyett a **és adatokat importálhat** modul az Azure Machine Learning Studióban elolvashatja a helyszíni SQL Server adatbázis-ről a képzés és a feladatok pontozási.

Ez a cikk ismerteti, hogyan érkező áttekintését a helyszíni SQL server-adatok az Azure Machine Learning. Feltételezi, hogy ismeri az Azure Machine Learning fogalmak munkaterületek, modulok, adatkészleteket, kísérleteket, például *stb*.

> [!NOTE]
> Ez a funkció nem érhető el a munkaterületek. További információ a Machine Learning díjszabás és rétegek: [Azure Machine Learning díjszabás](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>A Microsoft adatkezelési átjáró telepítése
Az Azure Machine Learning egy helyi SQL Server-adatbázis eléréséhez szükség töltse le és telepítse a Microsoft adatkezelési átjáró. Az átjáró kapcsolat konfigurálásakor a Machine Learning Studióban lehetősége van a töltse le és telepítse az átjáró használatával a **letöltés, és regisztrálja az adatátjárót** párbeszédpanel az alábbiakban.

Is elvégezheti a telepítést az adatkezelési átjáró időben letöltését és futtatását az MSI telepítő csomagot a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).
Válassza ki a legújabb verziót, 32 bites vagy 64 bites, a számítógép megfelelő. Az MSI-fájl egy meglévő adatkezelési átjárót a legújabb verzióra frissítésére megőrzi beállításokkal is használható.

Az átjáró előfeltételei a következők:

* A Windows operációs rendszer verziók a következők: Windows 7, Windows 8 vagy 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 és a Windows Server 2012 R2.
* Az ajánlott konfiguráció az átjárót működtető gépen legalább 2 GHz, 4 mag, 8GB RAM és 80GB lemezterület.
* A gazdaszámítógépen szeretnénk, ha az átjáró kérelmek nem válaszol. Ezért konfigurálni egy megfelelő energiaséma azon a számítógépen az átjáró telepítése előtt. Ha a számítógép hibernált állapotba van konfigurálva, az átjáró telepítésének üzenetet jelenít meg.
* Egy adott gyakorisággal másolási tevékenység során következik be, mert az az erőforrás-használat (Processzor, memória) a számítógépen is csúcs és üresjárati idő azonos mintát követi. Erőforrás-használat is függ, erősen áthelyezett adatok mennyiségét. Több másolási feladat van folyamatban, akkor lesz megfigyelheti csúcsidőben menni Erőforrás kihasználtsága. Bár technikailag elegendő a fent felsorolt minimális konfiguráció, érdemes lehet biztosítson több erőforrást, mint a minimális konfigurációs attól függően, hogy a meghatározott típusú adatátvitelt jelölik a konfigurációt.

Vegye figyelembe a következőket beállításával és használatával adatkezelési átjárót:

* Az adatkezelési átjáró csak egy példánya is telepíthető egy egyedi számítógép.
* Csak egyetlen átjáró használható több helyszíni adatforráshoz.
* Több átjáró különböző számítógépeken csatlakozhat a helyszíni ugyanazon az adatforráson.
* Egyszerre csak egy munkaterület átjárók konfigurálása. Jelenleg átjárók munkaterületek között is megoszthatja.
* Több átjáró egyetlen munkaterület konfigurálhatja. Érdemes lehet például egy átjáró, amely a fejlesztés során a teszt adatforrások csatlakozik és egy üzemi átjáró használatára, amikor azok készen.
* Az átjáró nem kell az adatforrással azonos gépen kell. De marad az adatforrás közelebb lerövidíti az átjáró által az adatforráshoz kapcsolódni. Azt javasoljuk, hogy az átjáró telepíthető olyan számítógépen, amelyen eltér a, amelyen a helyszíni adatforráshoz, hogy az átjáró és az adatforrás a nem "versenyeznek" az erőforrásokat.
* Ha már van egy átjáró a Power bi-ban vagy az Azure Data Factory forgatókönyvek szolgáltató számítógépre telepítve, telepítsen egy különálló átjáróra az Azure Machine Learning egy másik számítógépen.

  > [!NOTE]
  > Az adatkezelési átjáró és a Power BI-átjáró ugyanazon a számítógépen nem futtatható.
  >
  >
* Kell használnia az adatkezelési átjáró az Azure Machine Learning, még akkor is, ha az Azure ExpressRoute az egyéb adatokat használ. Kezelje az adatforrás egy helyszíni adatforrás (tűzfal mögött van), még akkor is ExpressRoute használatakor. Az adatkezelési átjáró használata a Machine Learning és az adatforrás közötti kapcsolat létrehozásához.

A cikkben található részletes információkat a telepítési előfeltételek, a telepítési lépések és a hibaelhárítási tippekért [az adatkezelési átjáró](../../data-factory/v1/data-factory-data-management-gateway.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Érkező adatokat az Azure Machine Learning a helyszíni SQL Server-adatbázisból
Ebben a bemutatóban adatkezelési átjárót beállítása az Azure Machine Learning-munkaterület, azt konfigurálása, és olvassa el az adatok egy helyi SQL Server-adatbázisból.

> [!TIP]
> Mielőtt elkezdené, tiltsa le a böngésző előugróablak-blokkoló a `studio.azureml.net`. Ha a Google Chrome böngészőt használ, letöltheti és telepítheti a több beépülő modulok érhető el a Google Chrome webes tároló egyik [kattintson egyszer bővítmény](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
>

### <a name="step-1-create-a-gateway"></a>1. lépés: Az átjáró létrehozása
Az első lépés, hogy hozzon létre és állítsa be az átjárót a helyszíni SQL-adatbázis eléréséhez.

1. Jelentkezzen be [Azure Machine Learning Studio](https://studio.azureml.net/Home/) válassza ki használni kívánt munkaterület.
2. Kattintson a **beállítások** a bal oldali panelen, majd a **DATA GATEWAYS** fülre az oldal tetején.
3. Kattintson a **az új ADATÁTJÁRÓ** a képernyő alján.

    ![Az új adatátjáró](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Az a **az új adatátjáró** párbeszédpanelen adja meg a **átjáró neve** , és opcionálisan adja hozzá a **leírása**. A konfiguráció a következő lépésre ugorhat jobb alsó sarokban látható nyílra kattintva.

    ![Adja meg az átjáró nevét és leírását](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. A letöltés és a regisztrációs adatok átjáró párbeszédpanelen az ÁTJÁRÓ regisztrációs kulcs másolása a vágólapra.

    ![Töltse le, és regisztrálja az adatátjárót](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Ha még nem letöltötte és telepítette a Microsoft adatkezelési átjáró, majd kattintson **letöltési az adatkezelési átjáró**. Ezzel megnyitná a Microsoft Download Center ahol válassza az átjáró verziója van szüksége, töltse le, és telepítse. A cikk elejére szakasza található részletes információkat a telepítési előfeltételek, a telepítési lépések és a hibaelhárítási tippekért [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró felhő között](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).
7. Miután az átjáró telepítve van, az adatkezelési átjáró konfigurációkezelőjének megnyílik, és a **átjáró regisztrálása** párbeszédpanel jelenik meg. Beillesztés a **átjáró regisztrációs kulcs** másolja a vágólapra, majd kattintson **regisztrálása**.
8. Ha már van egy telepített átjárót, futtassa az adatkezelési átjáró konfigurációkezelőjének. Kattintson a **kulcsváltás**, illessze be a **átjáró regisztrációs kulcs** , az előző lépésben a vágólapra, majd kattintson az **OK**.
9. Ha a telepítés befejeződött, a **Register átjáró** a Microsoft adatkezelési átjáró konfigurációkezelőjének párbeszédpanel jelenik meg. Illessze be az előző lépésben a vágólapra másolt ÁTJÁRÓ regisztrációs kulcs, és kattintson a **regisztrálása**.

    ![Átjáró regisztrálása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Az átjáró konfigurálása nem fejeződött be, amikor a következő értékek vannak megadva a **Home** lapon a Microsoft adatkezelési átjáró konfigurációkezelőjének:

    * **Átjáró neve** és **példánynév** azon átjáró neve van beállítva.
    * **Regisztrációs** értéke **regisztrált**.
    * **Állapot** értéke **elindítva**.
    * Az alsó jeleníti meg az állapotsorban **kapcsolódik az adatkezelési átjáró Felhőszolgáltatáshoz** mellett zöld pipa jelzi.

      ![Felügyeleti átjáró adatkezelő](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Az Azure Machine Learning Studio is frissül a Ha a regisztráció sikeres volt.

    ![Átjáró regisztrálása sikeres](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Az a **töltse le és adatátjáró regisztrálására** párbeszédpanel, kattintson a pipa jelre a telepítés befejezéséhez. A **beállítások** lapon megjelenik az átjáró állapotának "Elérhető". A jobb oldali ablaktáblában található állapotát és más hasznos információkat.

    ![Átjáró beállításai](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Az a Microsoft adatkezelési átjáró konfigurációkezelőjének váltani a **tanúsítvány** fülre. Ezen a lapon megadott tanúsítvány a helyi tárolót a portálon megadott hitelesítő adatok titkosításához/visszafejtéséhez használt. Ez a tanúsítvány az alapértelmezett tanúsítvány. A Microsoft azt javasolja, hogy ez a tanúsítvány felügyeleti rendszer biztonsági mentése a saját tanúsítványban módosítása. Kattintson a **módosítás** használhatja saját tanúsítvány is.

    ![Átjáró tanúsítvány módosítása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (választható) Ha engedélyezi a részletes naplózás elhárítása érdekében az átjáró, az a Microsoft adatkezelési átjáró konfigurációkezelőjének problémái váltani a **diagnosztika** fülre és ellenőrizze a **részletes naplózás engedélyezése hibaelhárítási célból** lehetőséget. A naplózási információkat a Windows Eseménynapló alatt található a **alkalmazási és Szolgáltatásnaplójában**  - &gt; **az adatkezelési átjáró** csomópont. Használhatja a **diagnosztika** fülre, és tesztelje a kapcsolatot az átjáró helyszíni adatforráshoz.

    ![Részletes naplózás engedélyezése](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Ezzel befejezte az átjáró telepítési folyamat az Azure Machine Learning.
Most már készen áll a helyszíni adatok használata.

Hozzon létre, és állítsa be az egyes munkaterületeken Studio több átjáró. Például előfordulhat, hogy egy átjáró, amely a fejlesztés során teszt adatforrásait csatlakozni kíván, és egy másik átjáró éles adatforrások. Az Azure Machine Learning szerződés keretein belül több átjáró attól függően, a vállalati környezet beállítása. Jelenleg egy átjáró munkaterületek között nem osztható meg, és csak egy átjáró telepíthető egy számítógépen. További információkért lásd: [helyezze át az adatokat a helyszíni adatforrások és az adatkezelési átjáró felhő között](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2. lépés: Az átjáró használatához egy helyszíni adatforrásból származó adatokat olvasni.
Az átjáró beállítása után hozzáadhat egy **és adatokat importálhat** modult egy, a helyszíni SQL Server-adatbázis adatait a bemenete.

1. A Machine Learning Studióban, válassza ki a **kísérletek** lapra, majd **+ új** a bal alsó sarkában, és válasszon **üres kísérlet** (vagy jelöljön ki egy több mintakísérleteket érhető el).
2. Keresse meg, és húzza a **és adatokat importálhat** modult a kísérletvászonra.
3. Kattintson a **Mentés másként** a vászon alatt. Adja meg az "Azure Machine Learning a helyszíni SQL Server oktatóanyag" kísérlet nevét, válassza ki a munkaterületet, és kattintson a **OK** pipára.

   ![Kísérlet mentése új néven](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kattintson a **és adatokat importálhat** rá kattintva jelölje ki, majd a modul a **tulajdonságok** panelen a vászontól jobbra "A helyszíni SQL adatbázis" jelölje ki a **adatforrás** legördülő listából.
5. Válassza ki a **adatátjáró** telepítve és regisztrálva. "(Hozzáadása az új adatátjáró...)" kiválasztásával állíthat be egy másik átjárót.

   ![Válassza ki az adatok importálása modul adatátjáró](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Adja meg az SQL **adatbázis-kiszolgáló nevét** és **adatbázisnév**, és az SQL **adatbázis-lekérdezés** szeretné végrehajtani.
7. Kattintson a **adja meg az értékeket** alatt **felhasználónevet és jelszót** és az adatbázis hitelesítő adatait. Használhatja az integrált Windows-hitelesítést vagy SQL Server-hitelesítés a helyszíni SQL Server konfigurációtól függően.

   ![Adja meg az adatbázis hitelesítő adatai](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   A "értékek beállítása" zöld pipa jelzi az üzenet "értékek a szükséges" megváltozik. Csak egyszer megadja a hitelesítő adatokat, kivéve, ha az adatbázis-információ vagy jelszavának megváltozását kell. Az Azure Machine Learning használja, ha telepítette az átjárót a felhőben a hitelesítő adatok titkosításához megadott tanúsítvány. Azure soha nem tárolja a helyszíni hitelesítő adatok titkosítás nélkül.

   ![Adattulajdonságainak modul importálása](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Kattintson a **futtatása** a kísérlet futtatásához.

Amennyiben az a kísérlet befejezése után történik, jelenítheti meg az adatokat az adatbázisból a kimeneti portjára kattintva importálja a **és adatokat importálhat** modult, majd válassza **Visualize**.

Miután befejezte a kísérletben fejleszt, telepítheti és üzembe helyezheti modelljét. A kötegelt végrehajtási szolgáltatás, a helyszíni SQL Server adatainak használatával adatbázis konfigurálva a **és adatokat importálhat** modul lesznek olvashatók és pontozó használt. A kérelem-válasz szolgáltatás használhatja a pontozási a helyszíni adatok, a Microsoft azt javasolja, használja a [Excel-bővítmény](excel-add-in-for-web-services.md) helyette. A helyszíni SQL Server adatbázis jelenleg írása **adatok exportálása** nem támogatott, vagy a kísérleti vagy a közzétett webes szolgáltatások.
