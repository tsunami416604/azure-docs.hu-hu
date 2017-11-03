---
title: "Közzététele, vagy telepítsen egy Azure alkalmazást a Visual Studio eszközből előkészítése |} Microsoft Docs"
description: "Ismerje meg, az eljárások a felhő- és tárolási fiók szolgáltatások beállítása és konfigurálása az Azure-alkalmazásában."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: cc4fb87e559f554634ae062a59bee31f0831da64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-to-publish-or-deploy-an-azure-application-from-visual-studio"></a>Felkészülés az közzététele, vagy az Azure alkalmazás a Visual Studio telepítése
## <a name="overview"></a>Áttekintés
Egy felhőszolgáltatás-projekt közzététele előtt be kell állítania a következő szolgáltatásokat:

* A **felhőalapú szolgáltatás** a szerepkörök az Azure környezetben való futtatásához
* A **tárfiók** , amely biztosítja, hogy a Blob, Queue és Table szolgáltatások eléréséhez.

Az alábbi eljárások segítségével állítsa be ezeket a szolgáltatásokat, és az alkalmazás konfigurálása

## <a name="create-a-cloud-service"></a>Felhőszolgáltatás létrehozása
Azure cloud Service szolgáltatásra közzétételéhez először létre kell hoznia egy felhőalapú szolgáltatás, amely a szerepkörök az Azure környezetben. Létrehozhat egy felhőszolgáltatás a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885)szakaszában leírtak szerint **egy felhőalapú szolgáltatás létrehozása a klasszikus Azure portál használatával**, ez a témakör későbbi részében. Egy felhőalapú szolgáltatás a Visual Studio közzététel a varázsló segítségével is létrehozhat.

### <a name="to-create-a-cloud-service-by-using-visual-studio"></a>Egy felhőalapú szolgáltatás létrehozása a Visual Studio használatával
1. Nyissa meg az Azure-projekt helyi menüjének, és válassza a **közzététel**.

    ![VST_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)
2. Ha még nem jelentkezett be, jelentkezzen be a felhasználónevét és jelszavát, a Microsoft-fiókkal vagy szervezeti fiókkal az Azure-előfizetéshez társított.
3. Válassza ki a **következő** ahhoz, hogy a gomb a **beállítások** lap.

    ![Közzétételi varázsló általános beállítások](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)
4. Az a **Felhőszolgáltatások** menüben válassza ki **hozzon létre új**. A **létrehozása Azure-szolgáltatások** párbeszédpanel jelenik meg.
5. Adja meg a felhőalapú szolgáltatás nevét. A név az URL-cím, a szolgáltatás részét képezi, és ezért egyedinek kell lenniük. A név nincs kis-és nagybetűket.

### <a name="to-create-a-cloud-service-by-using-the-azure-classic-portal"></a>Egy felhőalapú szolgáltatás létrehozása a klasszikus Azure portál használatával
1. Jelentkezzen be a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkId=253103) a Microsoft webhelyén.
2. (választható) Jelenítse meg a korábban létrehozott felhőalapú szolgáltatásokat, válassza a lap bal oldalán Felhőszolgáltatások hivatkozásra.
3. Válassza ki a  **+**  ikonra a bal alsó sarokban, és válassza a **Felhőszolgáltatás** a megjelenő menüben. Két lehetőség közül választhat, egy másik képernyő **Gyorslétrehozás** és **egyéni létrehozás**, akkor jelenik meg. Ha úgy dönt, **Gyorslétrehozás**, létrehozhat egy felhőalapú szolgáltatás csak az URL-CÍMÉT és megadásával a régió, ahol fizikailag tárolható. Ha úgy dönt, **egyéni létrehozás**, azonnal közzététele egy felhőszolgáltatás egy csomagot (.cspkg fájl), a konfigurációs (.cscfg) fájljában és tanúsítvány megadásával. Egyéni létrehozás nem szükséges, ha azt tervezi, közzététele a felhőalapú szolgáltatás segítségével a **közzététel** Azure-projekt parancsot. A **közzététel** parancs érhető el az Azure-projekt helyi menüje.
4. Válasszon **Gyorslétrehozás** később közzététele a felhőalapú szolgáltatás Visual Studio használatával.
5. Adjon meg egy nevet a felhőalapú szolgáltatás. A teljes URL-címet a neve mellett jelenik meg.
6. A listában válassza ki a régió, ahol a felhasználók a legtöbb találhatók.
7. Az ablak alján válassza ki a **felhőalapú szolgáltatás létrehozása** hivatkozásra.

## <a name="create-a-storage-account"></a>Create a storage account
A tárfiók a Blob, Queue és Table szolgáltatások hozzáférést biztosít. Visual Studio használatával létrehozhat egy tárfiókot, vagy a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkId=253103).

### <a name="to-create-a-storage-account-by-using-visual-studio"></a>A storage-fiók létrehozása a Visual Studio használatával
1. A **Megoldáskezelőben**, nyissa meg a helyi menüje a **tárolási** csomópont, és válassza a **Storage-fiók létrehozása**.

    ![Új Azure storage-fiók létrehozása](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)
2. Válassza ki vagy adja meg az új tárfiók a következő adatokat a **Storage-fiók létrehozása** párbeszédpanel megnyitásához.

   * Az Azure-előfizetés kívánt tárfiók hozzáadása.
   * Az új tárfiók használni kívánt nevét.
   * A régiót vagy affinitáscsoportot (például az USA nyugati régiója vagy Kelet-Ázsia).
   * A tárfiók, például a Georedundáns használni kívánt replikációs típusát.
3. Amikor elkészült, válassza ki a **létrehozása**. Az új tárfiók megjelenik a **tárolási** listájában **Server Explorer**.

### <a name="to-create-a-storage-account-by-using-the-azure-classic-portal"></a>A storage-fiók létrehozása a klasszikus Azure portál használatával
1. Jelentkezzen be a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkId=253103) a Microsoft webhelyén.
2. (Választható) A storage-fiókok megtekintéséhez válassza a **tárolási** hivatkozásra a lap bal oldalán a panelen.
3. A lap bal alsó sarkában, válassza ki a  **+**  ikonra.
4. A megjelenő menüben válassza a **tárolási**, és válassza a **Gyorslétrehozás**.
5. Nevezze el a tárfiók egyedi URL-cím eredményező.
6. Nevezze el a felhőalapú szolgáltatás. A teljes URL-címet a neve mellett jelenik meg.
7. Válassza ki a régió, ahol a felhasználók a legtöbb találhatók régiók listáját.
8. Adja meg, hogy szeretné-e a georeplikáció engedélyezéséhez. Ha a georeplikáció engedélyezéséhez az adatok elvesztését elkerülheti több fizikai helyen menti. A szolgáltatás révén tárolási drágább, de földrajzihely-engedélyezésével, a tárolási fiók helyett a szolgáltatás később létrehozásakor költségek csökkentése érdekében. További információkért lásd: [georeplikáció](http://go.microsoft.com/fwlink/?LinkId=253108).
9. Az ablak alján válassza ki a **Storage-fiók létrehozása** hivatkozásra.

A tárfiók létrehozása után jelenik meg az URL-címeket, az egyes az Azure storage szolgáltatások, valamint a fiók elsődleges és másodlagos elérési kulcsainak erőforrásainak elérésére használhat. Ezek a kulcsok használatával a tárolási szolgáltatások ellen kérelmek hitelesítéséhez szükséges.

> [!NOTE]
> A másodlagos elérési kulcsát hozzáférést biztosít a azonos a tárfiók elsődleges elérési kulcsát, és jön létre biztonsági az elsődleges elérési kulcsát utaló jeleket. Emellett ajánlott, hogy a tárelérési kulcsok rendszeresen újragenerálja. Módosíthatja a kapcsolati karakterlánc beállítása használható a másodlagos kulcs újragenerálja az elsődleges kulcsot úgy, hogy használja a rendszer újragenerálta elsődleges kulcs, miközben a másodlagos kulcs újragenerálása módosíthatja.
>
>

## <a name="configure-your-app-to-use-services-provided-by-the-storage-account"></a>Állítsa be alkalmazását a tárfiók által nyújtott szolgáltatások használatára
Olyan szerepkört, amely fér hozzá a tárolási szolgáltatások az Ön által létrehozott Azure storage szolgáltatásainak használatára kell konfigurálni. Ehhez az Azure-projekt több szolgáltatáskonfiguráció is használhat. Alapértelmezés szerint két jönnek létre az Azure-projekt. Több szolgáltatáskonfiguráció használatával használja ugyanazt a kapcsolati karakterláncot a kódban, de más értéket a kapcsolati karakterláncot kell minden egyes szolgáltatás konfigurációban. Használhatja például egy szolgáltatáskonfiguráció futtatásához és az alkalmazás helyileg az Azure storage emulator használatával hibakeresését és egy másik szolgáltatás konfigurációját az Azure-bA az alkalmazás közzétételére. Szolgáltatás-konfigurációkkal kapcsolatos további információkért lásd: [konfigurálása az Azure projekt használatával több szolgáltatáskonfiguráció](vs-azure-tools-multiple-services-project-configurations.md).

### <a name="to-configure-your-application-to-use-services-that-the-storage-account-provides"></a>Konfigurálhatja az alkalmazást, amely a tárfiók biztosít a szolgáltatásokhoz
1. A Visual Studióban nyissa meg az Azure megoldást. A Solution Explorerben nyissa meg a helyi menü az egyes szerepkörökhöz az Azure-projekt, aki hozzáfér a tárolási szolgáltatások, és válassza a **tulajdonságok**. A szerepkör nevét egy lapon megjelenik a Visual Studio-szerkesztőben. A lap megjeleníti a mezőket a **konfigurációs** fülre.
2. Válassza ki a szerepkörhöz a tulajdonságlapokon, **beállítások**.
3. Az a **szolgáltatáskonfiguráció** menüben válassza ki a szerkeszteni kívánt szolgáltatáskonfiguráció neve. Ha szeretné módosítani a szolgáltatáskonfiguráció a szerepkörhöz tartozó összes, választhat **összes konfiguráció**.  Szolgáltatáskonfiguráció frissítésével kapcsolatos további információkért tekintse meg a szakasz **Storage-fiókok kezelése kapcsolati karakterláncok** témakör [a Visual StudioAzureCloudServiceaszerepkörökkonfigurálása](vs-azure-tools-configure-roles-for-cloud-service.md).
4. A kapcsolódási karakterlánc beállításainak módosításához válassza ki a **...** gombra a beállítások mellett. A **tárolási kapcsolati karakterlánc létrehozása** párbeszédpanel jelenik meg.
5. A **protokoll használatával kapcsolódó levelezőprogramokkal**, válassza ki a **az előfizetés** lehetőséget.
6. Az a **előfizetés** menüben válassza ki az előfizetését. Ha az előfizetések listája nem tartalmazza a használni kívánt, válassza ki azt a **közzétételi beállítások letöltése** hivatkozásra.
7. Az a **fióknév** menüben válassza ki a tárfiók nevére. Azure-eszközök jut hozzá a tárfiók hitelesítő adatainak automatikusan a .publishsettings fájl használatával. A tárfiók hitelesítő adatainak manuális megadásához válassza a **manuálisan kell megadni a hitelesítő adatok** lehetőséget, majd folytassa az eljárás a. A tárfiók nevét és az elsődleges kulcs a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/p/?LinkID=213885). Ha nem kívánja adja meg a beállításokat manuálisan, válassza ki a tárfiók a **OK** gombra kattintva zárja be a párbeszédpanelt.
8. Válassza ki a **adja meg a tárfiók** hitelesítő adatok hivatkozásra.
9. Az a **fióknév** mezőbe írja be a tárfiók nevét.

   > [!NOTE]
   > Jelentkezzen be a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885), majd válassza a **tárolási** gombra. A portál storage-fiókok listáját tartalmazza. Ha úgy dönt, hogy a fiók, az oldal jelenik meg. Ezen a lapon másolhatja a tárfiók nevét. A klasszikus portálon korábbi verziójának használatakor a tárfiók neve megjelenik a **Tárfiókok** nézet. Másolja ezt a nevet, jelölje ki azt a a **tulajdonságok** ezen ablakban megtekintheti, és válassza a Ctrl-C kulcsok. Visual Studio illessze be a nevét, válassza ki a **fióknév** szöveg mezőbe, majd kattintson a Ctrl + V kulcsok.
   >
   >
10. Az a **fiókkulcs** mezőben, adja meg az elsődleges kulcs, vagy másolja be azt a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).
     Ez a kulcs másolása:

    1. Válassza ki a megfelelő tárfiók a lap alján a **kulcsok kezelése** gombra.
    2. Az a **kulcsok hozzáférés kezelése** lapon, válassza ki a szöveget az elsődleges elérési kulcsát, és válassza a Ctrl + C kulcsok.
    3. Illessze be a kulcsot az Azure eszközök, a **fiókkulcs** mezőbe.
    4. Választania kell meghatározni a szolgáltatás miként férhetnek hozzá a tárfiók a következő lehetőségek közül:

       * **A HTTP használata**. Ez a lehetőség a szabványos. Például: `http://<account name>.blob.core.windows.net`.
       * **A HTTPS PROTOKOLLT használja** a biztonságos kapcsolat. Például: `https://<accountname>.blob.core.windows.net`.
       * **Egyéni végpontokat határoz meg** az egyes mindhárom szolgáltatást. Majd az adott szolgáltatás mezőbe írja be ezeket a végpontokat.

         > [!NOTE]
         > Ha egyéni végpontokat hoz létre, létrehozhat egy összetettebb kapcsolati karakterláncot. Ez a karakterlánc-formátum használata esetén a tárfiók a Blob szolgáltatásban regisztrált egyéni tartománynevet tartalmazó tároló Szolgáltatásvégpontok is megadhat. Csak egyetlen tárolót a közös hozzáférésű jogosultságkód keresztül blob erőforrásaihoz való hozzáférés is megadhatók. Egyéni végpontokat létrehozásával kapcsolatos további információkért lásd: [konfigurálása Azure Storage kapcsolati karakterláncok](storage/common/storage-configure-connection-string.md).
         >
         >
11. A kapcsolati karakterlánc változtatások mentéséhez kattintson a **OK** gombra, majd válassza ki a **mentése** gomb az eszköztáron. A módosítások mentése után olvashatók be a kapcsolati karakterlánc értékét a kód használatával [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Ha közzéteszi a az alkalmazás az Azure-ba, válassza a szolgáltatás konfigurációja, amely tartalmazza az Azure storage-fiók a kapcsolódási karakterláncban. Az alkalmazás közzététele után győződjön meg arról, hogy az alkalmazás megfelelően működik-e az Azure storage szolgáltatásainak ellen

## <a name="next-steps"></a>Következő lépések
Tudjon meg többet a közzétételi alkalmazások az Azure-bA a Visual Studio eszközből, lásd: [közzététele a felhőalapú szolgáltatás, az Azure-eszközökkel](vs-azure-tools-publishing-a-cloud-service.md).
