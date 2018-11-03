---
title: Felügyelheti az Azure IoT Central alkalmazáshoz |} A Microsoft Docs
description: Egy rendszergazdaként az Azure IoT központi alkalmazás felügyelete
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4bfb7dc2b65dc479189ac7920509e1fd8a23ce4f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961347"
---
# <a name="administer-your-iot-central-application"></a>Az IoT-központ alkalmazás felügyelete

Miután létrehozott egy IoT-központ alkalmazás, nyissa meg a **felügyeleti** szakaszt:

- Alkalmazásbeállítások kezelése
- Felhasználók kezelése
- Szerepkörök kezelése
- A számla megtekintése
- A próbaidőszak váltson használatalapú fizetésre
- Adatok exportálása
- Eszköz kapcsolat kezelése
- Hozzáférési kódok használata

Hogy eléri és használja a **felügyeleti** szakaszban kell lennie a a **rendszergazda** szerepkört az Azure IoT Central alkalmazáshoz. Azure IoT Central alkalmazást hoz létre, ha automatikusan kapott a **rendszergazda** szerepkör az adott alkalmazáshoz. A [felhasználók kezelése](#manage-users) szakasz ebben a cikkben a hozzárendelése több ismerteti a **rendszergazda** szerepkör más felhasználók számára.

## <a name="manage-application-settings"></a>Alkalmazásbeállítások kezelése

### <a name="change-application-name-and-url"></a>Alkalmazás nevének módosítása és URL-címe
Az a **Alkalmazásbeállítások** lapon módosítsa a nevét és az alkalmazás URL-címet, majd válassza ki **mentése**.

![Alkalmazás-Beállítások lap](media\howto-administer\image0-a.png)

> [!Note]
> Ha megváltoztatja az URL-CÍMÉT, a régi URL-CÍMÉT egy másik Azure IoT Central ügyfél elvégezhet. Ha ez történik, már nem érhető el, amelyet használhat. Ha megváltoztatja az URL-CÍMÉT, a régi URL-cím már nem működik, és értesíti a felhasználókat arról, hogy az új URL-cím használatára kell.

### <a name="prepare-and-upload-image"></a>Készítse elő és a Rendszerkép feltöltése
Ha módosítani szeretné az alkalmazás rendszerképét, lásd: [előkészítése és a feltöltés rendszerképek az Azure IoT Central alkalmazásnak](howto-prepare-images.md).

### <a name="copy-an-application"></a>Alkalmazás másolása
Minden olyan alkalmazás, bármely eszköz példányok, az adatok eszközelőzmények és a felhasználói adatok mínusz egy másolatát is létrehozhat. A példány lesz egy használatalapú fizetéses alkalmazást, amely akkor kell fizetnie. Ezzel a módszerrel egy próba-alkalmazás nem hozható létre.

Kattintson a **másolási** gombra. A párbeszédpanelen adja meg, hogy az új használatalapú fizetéses alkalmazás. Kattintson a **másolási** gombra annak megerősítéséhez, hogy végre szeretné hajtani. További információ a mezőket az űrlapon található [hozzon létre egy alkalmazást](quick-deploy-iot-central.md) rövid.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy2.png)

Miután az alkalmazás másolási művelet sikeres, az új alkalmazást, az alkalmazás használatával, amely megjelenik a hivatkozás másolása által létrehozott léphet.

![Alkalmazás-Beállítások lap](media\howto-administer\appCopy3.png)

> [!Note]
> Alkalmazás másolása is átmásolja a szabályok és műveletek definíciója. De az eredeti alkalmazáshoz hozzáféréssel rendelkező felhasználók nem másolja át a másolt alkalmazást, mert a felhasználók manuális hozzáadása műveleteket, például e-mail, amelyhez felhasználók előfeltétele. Általában célszerű a szabályokat és műveleteket, győződjön meg arról, hogy azok az új alkalmazás naprakész, ellenőrizze, hogy legyen.

### <a name="delete-an-application"></a>Alkalmazás törlése

Használja a **törlése** gombra kattintva véglegesen törli az IoT Central alkalmazáshoz. Ezzel véglegesen törli az adott alkalmazáshoz társított összes adatot. Egy alkalmazás törléséhez, jogosultnak kell lennie is törölje az erőforrást az Azure-előfizetésében választotta, az alkalmazás létrehozásakor. További tudnivalókért lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-users"></a>Felhasználók kezelése

### <a name="add-users"></a>Felhasználók hozzáadása

Minden rendelkezniük kell egy felhasználói fiókkal jelentkezzen be, és hozzáférést az Azure IoT Central alkalmazáshoz. A Microsoft Accounts (msa-k) és az Azure Active Directory (Azure AD) az Azure IoT Central támogatottak. Az Azure Active Directory-csoportok jelenleg nem támogatottak az Azure IoT Central.

További információkért lásd: [Microsoft-fiók súgó](https://support.microsoft.com/products/microsoft-account?category=manage-account) és [a rövid útmutató: új felhasználók hozzáadása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Egy felhasználó IoT Central alkalmazáshoz való hozzáadásához nyissa meg a **felhasználók** lapját a **felügyeleti** szakaszban.

    ![Felhasználók listája](media\howto-administer\image1.png)

1. A felhasználó hozzáadásához a **felhasználók** lapon a **+ Hozzáadás felhasználó**.

1. Válassza ki a felhasználót egy szerepkört a **szerepkör** legördülő menüből. További információ a szerepkörökről az [szerepkörök kezelése](#manage-roles) című szakaszát.

    ![Szerepkör kiválasztása](media\howto-administer\image3.png)

    > [!NOTE]
    >  Felhasználók tömeges hozzáadása, a felhasználó az összes olyan a hozzáadni kívánt felhasználó azonosítóját adja meg pontosvesszővel elválasztva. A munkakör kiválasztása a **szerepkör** legördülő menüből. Ezután válassza a **Save** (Mentés) lehetőséget.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>A felhasználókhoz rendelt szerepkörök szerkesztése

Szerepkörök nem módosítható, miután hozzá vannak rendelve. A felhasználóhoz hozzárendelt szerepkör módosítása, törölje a felhasználót, és adja hozzá a felhasználót újra egy másik szerepkörrel rendelkező.

### <a name="delete-users"></a>Felhasználók törlése

Törli a felhasználókat, válassza ki egy vagy több jelölőnégyzetet az **felhasználók** lap. Ezután válassza a **Törlés** elemet.

## <a name="manage-roles"></a>Szerepkörök kezelése

Szerepkörök lehetővé teszi annak vezérléséhez a szervezeten belül az IoT-központ különböző feladatok végrehajtására. Nincsenek három szerepkört hozzárendelheti az alkalmazást. 

### <a name="administrator"></a>Rendszergazda

A felhasználók a **rendszergazda** szerepkör hozzáférése az összes funkciója egy alkalmazásban.

A felhasználó, aki alkalmazást hoz létre automatikusan hozzá van rendelve a **rendszergazda** szerepkör. Mindig kell legalább egy felhasználója a **rendszergazda** szerepkör.

### <a name="application-builder"></a>Alkalmazásszerkesztő

A felhasználók a **alkalmazás Builder** szerepkör műveletek mindegyikét egy alkalmazásban, kivéve az alkalmazás felügyeletét. Ez azt jelenti, hogy sikerei is létrehozásához, szerkesztéséhez és eszközsablonok és eszközök törlése, eszköz készletek felügyelete és analytics és a feladatok futtatásához. Kapcsolat építői többé nem fér hozzá a **felügyeleti** szakaszban az alkalmazás.

### <a name="application-operator"></a>Alkalmazás-operátor

A felhasználók a **alkalmazást üzemeltető** szerepkör nem hajthat végre változtatásokat eszközsablonok és az alkalmazás nem tudja felügyelni. Ez azt jelenti, operátorok is hozzáadások és törlések eszközök, eszköz-készletek felügyelete és analytics és a feladatok futtatásához. Operátorok többé nem fér hozzá a **alkalmazás Builder** és **felügyeleti** oldalakat.


## <a name="view-your-bill"></a>A számla megtekintése

A számla megtekintéséhez nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. Az Azure számlázási oldalán, ahol megtekintheti a számla minden, az Azure IoT Central alkalmazások egy új lapon nyílik meg.

### <a name="convert-your-trial-to-pay-as-you-go"></a>A próbaidőszak váltson használatalapú fizetésre

Átválthat egy használatalapú fizetéses alkalmazás próbaverzió alkalmazását. Az alábbiakban az ilyen típusú alkalmazások közötti különbségeket.

- **Próbaverzió** alkalmazások ingyenesek 7 napig még a lejárat előtt. Ezek konvertálhatók használatalapú fizetéses előfizetésre még a lejárat előtt bármikor.
- **Használatalapú fizetés** alkalmazások után felszámított díjak eszközt, az első 5 ingyenes eszközökkel.

A díjszabással kapcsolatos további tudnivalókért a [Azure IoT Central díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/iot-central/).
    
Az önkiszolgáló folyamat befejezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a **számlázási** lapját a **felügyeleti** szakaszban. 

    ![Próbaverziós állapota](media/howto-administer/freetrialbilling.png)

1. Kattintson a **utólagos elszámolású előfizetésre átváltani**. 

    ![Átalakítás próba](media/howto-administer/convert.png)

1. Válassza ki a megfelelő Azure Active Directoryban, majd az Azure-előfizetés az utólagos elszámolású alkalmazás használatához.

1. Miután rákattintott **konvertálása**, az alkalmazás most már egy használatalapú fizetéses alkalmazást, és számlázandó megkezdése.

## <a name="export-data"></a>Adatok exportálása

Engedélyezheti a **folyamatos adatexportálás** mérések, eszközök és eszközadatok sablonok exportálása az Azure Blob storage-fiókot. Tudjon meg többet [az adatok exportálása](#howto-export-data).

## <a name="manage-device-connection"></a>Eszköz kapcsolat kezelése

Csatlakozás az alkalmazásban, a kulcsok és tanúsítványok ide használatával nagy mennyiségű eszközt. Tudjon meg többet [eszközök csatlakoztatásáról](#concepts-connectivity).

## <a name="use-access-tokens"></a>Hozzáférési kódok használata

A fejlesztői eszközök használandó hozzáférési jogkivonatokat hoz létre. Jelenleg nincs egy fejlesztői eszköz, amely egyben az IoT-központ explorer figyeléshez az eszközre és változások a propreties és a beállítások. Tudjon meg többet a [IoT-központ explorer](#howto-use-iotc-explorer). 

## <a name="use-the-azure-sdks-for-control-plane-operations"></a>Az Azure SDK-k használata vezérlési síkjával végzett műveletek

A Node, Python, C#, Ruby, a Java és Go IoT Central Azure Resource Manager SDK-csomagok érhetők el. E kódtárak támogatási vezérlési síkjával végzett műveletek az IoT-központ, így lehetővé teszi a létrehozása, listázása, frissítésére, vagy törölje az IoT Central-alkalmazást. Hitelesítési többé vesződnie a sérült segítők is rendelkeznek, és a hibakezelési, amely csak a különböző nyelveken. 

Példa bemutatja, hogyan használhatja az Azure Resource Manager SDK-k, annak [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

További tudnivalókért tekintse meg ezeket a csomagokat a Githubon.

| Nyelv | Tárház | Csomag |
| ---------| ---------- | ------- |
| Csomópont | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Indítás | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT központi alkalmazás felügyelete, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az eszköz-sablon beállítása](howto-set-up-template.md)
