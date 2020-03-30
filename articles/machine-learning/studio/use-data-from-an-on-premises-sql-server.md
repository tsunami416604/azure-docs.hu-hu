---
title: Helyszíni SQL Server
titleSuffix: ML Studio (classic) - Azure
description: A helyszíni SQL Server-adatbázisból származó adatok használatával speciális elemzéseket végezhet az Azure Machine Learning Studio (klasszikus) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204182"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Elemzés végrehajtása az Azure Machine Learning Studio -val (klasszikus) egy helyszíni SQL Server-adatbázis használatával

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

A helyszíni adatokkal dolgozó vállalatok gyakran szeretnék kihasználni a felhő méretét és a gépi tanulási számítási feladatainak rugalmasságát. De nem akarják megzavarni a jelenlegi üzleti folyamatokat és munkafolyamatokat azáltal, hogy a helyszíni adatokat a felhőbe helyezik át. Az Azure Machine Learning Studio (klasszikus) mostantól támogatja az adatok helyszíni SQL Server-adatbázisból történő olvasását, majd a modell betanítását és pontozását ezekkel az adatokkal. A továbbiakban nem kell manuálisan másolnia és szinkronizálnia az adatokat a felhő és a helyszíni kiszolgáló között. Ehelyett az **Adatok importálása** modul az Azure Machine Learning Studio (klasszikus) most már közvetlenül a helyszíni SQL Server-adatbázis a képzési és pontozási feladatok.

Ez a cikk áttekintést nyújt a helyszíni SQL-kiszolgáló adatainak az Azure Machine Learning Studio (klasszikus) be- és bekerüléséről. Feltételezi, hogy ismeri a Studio (klasszikus) fogalmait, például a munkaterületeket, modulokat, adatkészleteket, kísérleteket *stb.*

> [!NOTE]
> Ez a funkció nem érhető el ingyenes munkaterületeken. A Machine Learning díjszabásáról és a szintekről az [Azure Machine Learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/)című témakörben talál további információt.
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>A Data Factory saját üzemeltetésű integrációs futásidejűének telepítése
A helyszíni SQL Server-adatbázis eléréséhez az Azure Machine Learning Studio (klasszikus), le kell töltenie és telepítenie kell a Data Factory self-hosted integration runtime, korábbi nevén az adatkezelési átjáró. Ha a kapcsolatot a Machine Learning Studio (klasszikus) programban konfigurálja, az alábbiakban ismertetett **Adatátjáró letöltése és regisztrálása** párbeszédpanelen letöltheti és telepítheti az integrációs futásidejűt (IR).


Az infravörös rendszert a [Microsoft letöltőközpontból](https://www.microsoft.com/download/details.aspx?id=39717)történő letöltéssel és futtatásával is előre telepítheti. Az MSI egy meglévő infravörös rendszer legújabb verziójára is frissíthető, az összes beállítás megőrzésével.

A Data Factory saját üzemeltetésű integrációs futásideje a következő előfeltételekkel rendelkezik:

* A data factory self-hosted integrációhoz 64 bites operációs rendszerre van szükség.
* A Windows operációs rendszer támogatott verziói a Következők: Windows 10 , Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Az infravörös gép ajánlott konfigurációja legalább 2 GHz, 4 magos CPU, 8 GB RAM és 80 GB lemez.
* Ha a gazdagép hibernált állapotban van, az infravörös rendszer nem válaszol az adatkérésekre. Ezért az infravörös modul telepítése előtt konfigurálja a megfelelő energiasémát a számítógépen. Ha a számítógép hibernálásra van konfigurálva, az infravörös telepítés üzenetet jelenít meg.
* Mivel a másolási tevékenység egy adott gyakorisággal történik, a számítógépen lévő erőforrás-használat (CPU, memória) is ugyanazt a mintát követi, csúcs- és tétlen idővel. Az erőforrások kihasználtságátis nagymértékben függ az áthelyezett adatok mennyiségétől. Ha több másolási feladat van folyamatban, megfigyelheti, hogy az erőforrás-használat csúcsidőben felfelé halad. Bár a fent felsorolt minimális konfiguráció technikailag elegendő, érdemes lehet egy konfiguráció, amely több erőforrást, mint a minimális konfiguráció az adott terhelés adatáthelyezési.

Vegye figyelembe az alábbiakat a Data Factory self-hosted integration runtime beállításakor és használatakor:

* Az infravörös modulnak csak egy példányát telepítheti egyetlen számítógépre.
* Egyetlen infravörös kapcsolat on-premises adatforrásokhoz is használható.
* Különböző számítógépeken több irs-t is csatlakoztathat ugyanahhoz a helyszíni adatforráshoz.
* Egyszerre csak egy munkaterületre konfigurálhat be egy irs-t. Jelenleg az irs nem oszthatók meg a munkaterületeken.
* Egyetlen munkaterülethez több irs-t is konfigurálhat. Előfordulhat például, hogy a fejlesztés során a tesztadatforrásokhoz csatlakoztatott infravörös kapcsolatát, a termelési infravörös rendszert pedig akkor érdemes használnia, ha készen áll a működésbe.
* Az infravörös modulnak nem kell ugyanazon a gépen lennie, mint az adatforrásnak. De ha közelebb marad az adatforráshoz, csökken az idő, hogy az átjáró csatlakozzon az adatforráshoz. Azt javasoljuk, hogy telepítse az infravörös egy olyan számítógépre, amely eltér attól, amely a helyszíni adatforrást üzemelteti, hogy az átjáró és az adatforrás ne versenyezzenek az erőforrásokért.
* Ha már telepítve van egy beépített rendszer a Power BI vagy az Azure Data Factory-forgatókönyveket kiszolgáló számítógépen, telepítsen egy külön infravörös az Azure Machine Learning Studio (klasszikus) egy másik számítógépre.

  > [!NOTE]
  > A Data Factory self-hosted integration runtime és a Power BI Gateway nem futtatható ugyanazon a számítógépen.
  >
  >
* A Data Factory self-hosted Integration Runtime for Azure Machine Learning Studio (klasszikus) használatát akkor is használnia kell, ha az Azure ExpressRoute-ot más adatokhoz használja. Az adatforrást helyszíni adatforrásként kell kezelnie (amely tűzfal mögött van), még akkor is, ha ExpressRoute-ot használ. A Data Factory self-hosted integration runtime segítségével kapcsolatot létesítanek a Machine Learning és az adatforrás között.

A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről részletes információkat az [Integrációs futásidő a Data Factory-ban](../../data-factory/concepts-integration-runtime.md)című cikkben talál.

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Adatok be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és beírása a helyszíni SQL Server-adatbázisból az Azure Machine Learning befoly
Ebben a forgatókönyvben egy Azure Data Data-integrációs futásidőt állít be egy Azure Machine Learning-munkaterületen, konfigurálja, majd adatokat olvas fel egy helyszíni SQL Server-adatbázisból.

> [!TIP]
> Mielőtt elkezdené, tiltsa le a böngésző `studio.azureml.net`előugró ablakokat blokkoló ját. Ha a Google Chrome böngészőt használja, töltse le és telepítse a Google Chrome WebStore Click Once App Extension webhelyen elérhető számos bővítmény [egyikét.](https://chrome.google.com/webstore/search/clickonce?_category=extensions)
>
> [!NOTE]
> Az Azure Data Factory saját üzemeltetésű integrációs futásidejű korábbi nevén Adatkezelési átjáró. A lépésről lépésre bemutató továbbra is hivatkoznak rá, mint egy átjáró.  

### <a name="step-1-create-a-gateway"></a>1. lépés: Átjáró létrehozása
Az első lépés a helyszíni SQL-adatbázis eléréséhez az átjáró létrehozása és beállítása.

1. Jelentkezzen be az [Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net/Home/) gépi stúdióba, és válassza ki azt a munkaterületet, amelyben dolgozni szeretne.
2. Kattintson a bal oldali **BEÁLLÍTÁSOK** panelre, majd a lap tetején a **DATA GATEWAYS** fülre.
3. Kattintson az **ÚJ ADATÁTJÁRÓ** gombra a képernyő alján.

    ![Új adatátjáró](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Az **Új adatátjáró** párbeszédpanelen adja meg az **átjáró nevét,** és adja meg a **Leírást.** Kattintson a jobb alsó sarokban lévő nyílra a konfiguráció következő lépésének lépéséhez.

    ![Adja meg az átjáró nevét és leírását](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Az Adatátjáró letöltése és regisztrálása párbeszédpanelen másolja a GATEWAY REGISTRATION KULCSOT a vágólapra.

    ![Adatátjáró letöltése és regisztrálása](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Ha még nem töltötte le és nem telepítette a Microsoft Data Management Gateway-t, kattintson az **Adatkezelési átjáró letöltése gombra.** Ezzel a Microsoft letöltőközpontba kerül, ahol kiválaszthatja a szükséges átjáróverziót, letöltheti és telepítheti azt. A telepítési előfeltételekről, a telepítési lépésekről és a hibaelhárítási tippekről részletes információkat a cikk első szakaszaiban talál, amelyek [az adatok áthelyezése a helyszíni források és a felhő között az Adatkezelési átjáróval](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Az átjáró telepítése után megnyílik az Adatkezelési átjáró konfigurációkezelője, és megjelenik az **Átjáró regisztrálása** párbeszédpanel. Illessze be a vágólapra másolt **átjáróregisztrációs kulcsot,** és kattintson a **Regisztráció gombra.**
8. Ha már van telepítve átjáró, futtassa az Adatkezelési átjáró konfigurációkezelőjét. Kattintson a **Kulcs módosítása gombra,** illessze be az előző lépésben a vágólapra másolt **átjáróregisztrációs kulcsot,** majd kattintson az **OK**gombra.
9. Amikor a telepítés befejeződött, megjelenik a Microsoft Data Management Gateway Configuration Manager **átjáróregisztráláspárbeszédpanele.** Illessze be az előző lépésben a vágólapra másolt ÁTJÁRÓ REGISZTRÁCIÓS KULCSOT, és kattintson a **Regisztráció gombra.**

    ![Átjáró regisztrálása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Az átjáró konfigurációja akkor fejeződik be, ha a Microsoft Data Management Gateway Configuration Manager **Kezdőlap** lapján a következő értékek vannak beállítva:

    * **Az átjáró neve** és **a példány neve** az átjáró nevére van beállítva.
    * **A regisztráció** **beállítása Regisztrált**.
    * **Az állapot** **Elindítva**.
    * Az alsó állapotsor a **Connected to Data Management Gateway Cloud Service szolgáltatáshoz** egy zöld pipával együtt jelenik meg.

      ![Adatkezelési átjárókezelő](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Az Azure Machine Learning Studio (klasszikus) is frissül, ha a regisztráció sikeres.

    ![Az átjáró regisztrációja sikeres](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Az **adatátjáró letöltése és regisztrálása** párbeszédpanelen a beállítás befejezéséhez jelölje be a négyzetet. A **Beállítások** lapon az átjáró állapota "Online". A jobb oldali ablaktáblában állapot- és egyéb hasznos információkat talál.

    ![Átjáró beállításai](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. A Microsoft Data Management Gateway Konfigurációkezelőjében váltson a **Tanúsítvány** lapra. Az ezen a lapon megadott tanúsítvány a portálon megadott helyszíni adattár hitelesítő adatainak titkosítására/visszafejtésére szolgál. Ez a tanúsítvány az alapértelmezett tanúsítvány. A Microsoft azt javasolja, hogy módosítsa ezt a tanúsítványkezelő rendszerben biztonsági másolatot tartalmazó saját tanúsítványra. Kattintson a **Módosítás** gombra a saját tanúsítvány használatához.

    ![Átjárótanúsítvány módosítása](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (nem kötelező) Ha engedélyezni szeretné a részletes naplózást az átjáróval kapcsolatos problémák elhárításához, a Microsoft Data Management Gateway **Konfigurációkezelőjében váltson a Diagnosztika** lapra, és ellenőrizze a **Részletes naplózás engedélyezése hibaelhárítási célokra jelölőnégyzetet.** A naplózási adatok a Windows Eseménynaplóban, az **Alkalmazások és szolgáltatások adatkezelési**  - &gt; **átjárócsomópontja** alatt találhatók. A **Diagnosztika** lapon tesztelheti a kapcsolatot egy helyszíni adatforrással az átjáró használatával.

    ![Részletes naplózás engedélyezése](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Ez befejezi az átjáró beállítási folyamataz Azure Machine Learning Studio (klasszikus).
Most már készen áll a helyszíni adatok használatára.

A Studio -ban (klasszikus) több átjárót hozhat létre és állíthat be minden munkaterülethez. Előfordulhat például, hogy rendelkezik egy átjáróval, amelyet a fejlesztés során csatlakozni szeretne a tesztadatforrásokhoz, és egy másik átjárót a termelési adatforrásokhoz. Az Azure Machine Learning Studio (klasszikus) rugalmasságot biztosít több átjáró beállításához a vállalati környezettől függően. Jelenleg nem lehet megosztani egy átjárót a munkaterületek között, és csak egy átjáró telepíthető egyetlen számítógépre. További információt az [Adatok áthelyezése a helyszíni források és a felhő között az Adatkezelési átjáróval című témakörben talál.](../../data-factory/tutorial-hybrid-copy-portal.md)

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>2. lépés: Az átjáró használatával adatokat olvashat be egy helyszíni adatforrásból
Az átjáró beállítása után hozzáadhat egy **Adatok importálása** modult egy kísérlethez, amely a helyszíni SQL Server-adatbázisból adja be az adatokat.

1. A Machine Learning Studio (klasszikus) alkalmazásban válassza a **KÍSÉRLETEK** lapot, kattintson a **+ÚJ** gombra a bal alsó sarokban, és válassza az **Üres kísérlet** lehetőséget (vagy válasszon egyet a rendelkezésre álló számos mintakísérlet közül).
2. Keresse meg és húzza az **Adatok importálása** modult a kísérlet vászonra.
3. Kattintson a **Mentés a** vászon alatt. Írja be az "Azure Machine Learning Studio (klasszikus) helyszíni SQL Server oktatóanyag" kifejezést a kísérlet nevéhez, válassza ki a munkaterületet, és kattintson az **OK** jelölőnégyzetbe.

   ![Kísérlet mentése új névvel](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Kattintson az **Adatok importálása** modulra a kijelöléséhez, majd a vászon jobb oldalán található **Tulajdonságok** ablaktáblán válassza a "Helyszíni SQL-adatbázis" lehetőséget az **Adatforrás legördülő** listában.
5. Válassza ki a telepített és regisztrált **adatátjárót.** Az "(új adatátjáró hozzáadása...)" lehetőség kiválasztásával másik átjárót is beállíthat.

   ![Adatátjáró kijelölése az Adatok importálása modulhoz](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Adja meg az SQL **Database-kiszolgáló nevét** és **az Adatbázis nevét,** valamint a végrehajtani kívánt SQL **Database lekérdezést.**
7. Kattintson **az Értékek megadása** a **Felhasználónév és jelszó** csoportban, és adja meg az adatbázis hitelesítő adatait. A Helyszíni SQL Server konfigurálásától függően használhatja a Windows integrált hitelesítést vagy az SQL Server-hitelesítést.

   ![Adatbázis hitelesítő adatainak megadása](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Az "szükséges értékek" üzenet zöld pipával "beállított értékekre" változik. Csak egyszer kell megadnia a hitelesítő adatokat, kivéve, ha az adatbázis adatai vagy a jelszó megváltozik. Az Azure Machine Learning Studio (klasszikus) az átjáró telepítésekor megadott tanúsítványt használja a hitelesítő adatok titkosításához a felhőben. Az Azure soha nem tárolja a helyszíni hitelesítő adatoktitkosítás nélkül.

   ![Adatok modul tulajdonságainak importálása](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. A kísérlet futtatásához kattintson a **FUTTATÁS** gombra.

A kísérlet futásának befejezése után az adatbázisból importált adatokat az **Adatok importálása** modul kimeneti portjára kattintva és a **Visualize parancsra**kattintva jelenítheti meg.

Miután befejezte a kísérlet fejlesztését, üzembe helyezheti és üzembe helyezheti a modellt. A Batch Execution Service használatával a helyszíni SQL Server-adatbázisból származó, az **Adatok importálása** modulban konfigurált adatokat a rendszer beolvassa és felhasználja a pontozáshoz. Bár használhatja a kérelem-válasz szolgáltatás a helyszíni adatok pontozásához, a Microsoft azt javasolja, hogy használja az [Excel bővítmény](excel-add-in-for-web-services.md) helyett. Jelenleg a helyszíni SQL Server-adatbázisba az **Adatok exportálása** szolgáltatáson keresztül történő írása sem a kísérletekben, sem a közzétett webszolgáltatásokban nem támogatott.
