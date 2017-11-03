---
title: "Azure-tárfiók integrálása az Azure CDN |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure tartalom Delivery Network (CDN) tartalmak nagy sávszélességű kézbesítéséhez az Azure Storage blobs gyorsítótárazása révén."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure-tárfiók integrálása az Azure CDN szolgáltatás használata
CDN az az Azure storage engedélyezhető a gyorsítótár teljes tartalmát. A fejlesztők a tartalmak nagy sávszélességű kézbesítéséhez a blobok és számítási példányokért fizikai csomópontokon az Egyesült Államok, Európa, Ázsia, Ausztrália és Dél-Amerika a statikus tartalom gyorsítótárazása révén globális megoldást kínál.

## <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása
A következő eljárással hozzon létre egy új tárfiókot, Azure-előfizetéssel. A storage-fiók az Azure storage-szolgáltatásokhoz való hozzáférést. A tárfiók eléréséhez szükséges az Azure storage szolgáltatás összetevői a névtér a legmagasabb szintű jelöli: Blob-szolgáltatások, Queue szolgáltatások és Table szolgáltatások. További információkért tekintse meg a [Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md).

Hozzon létre egy tárfiókot, vagy a szolgáltatás rendszergazdájának vagy társadminisztrátorának a társított előfizetés kell lennie.

> [!NOTE]
> Többféleképpen hozzon létre egy tárfiókot, beleértve az Azure portál és a Powershell segítségével.  Ebben az oktatóanyagban használni fogjuk az Azure portálon.  
> 
> 

**A storage-fiók egy Azure-előfizetés létrehozása**

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza ki a bal felső sarokban, **új**. Az a **új** párbeszédpanelen válassza **adatok + tárolás**, majd kattintson a **tárfiók**.
    
    A **storage-fiók létrehozása** panel jelenik meg.   

    ![Storage-fiók létrehozása][create-new-storage-account]  

3. Az a **neve** mezőbe írja be egy altartomány nevét. Ez a bejegyzés 3-24 kisbetűket és számokat tartalmazhat.
   
    Ez az érték lesz az állomásnév, az előfizetés Blob, sor vagy tábla erőforrásainak címzéséhez használt URI-Azonosítóra belül. Egy tároló-erőforrás a Blob szolgáltatás megoldására használhatja egy URI-t a következő formátumban, ahol  *&lt;StorageAccountLabel&gt;*  hivatkozik a beírt érték **adjon meg egy URL-cím**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Fontos:** az URL-cím címke űrlapok a tárfiók URI altartomány, és az Azure-ban az összes üzemeltetett szolgáltatások egyedinek kell lennie.
   
    Ezt az értéket is használja a tárfiók a portálon, vagy neveként ezt a fiókot programozott módon való hozzáférés során.
4. Hagyja meg az alapértelmezett értéket **telepítési modell**, **fiók kind**, **teljesítmény**, és **replikációs**. 
5. Válassza ki a **előfizetés** , amely a tárolási fiók használandó.
6. Válasszon ki vagy hozzon létre egy **erőforráscsoportot**.  További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Válasszon egy helyet a tárfiók.
8. Kattintson a **Create** (Létrehozás) gombra. A storage-fiók létrehozása eltarthat néhány percet.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>2. lépés: A tárfiók CDN engedélyezése

Az a legújabb integrációs most engedélyezheti a CDN a tárfiók anélkül, hogy a tároló portálbővítményt. 

1. Válassza ki a tárfiókot, keressen a "CDN" vagy a bal oldali navigációs menü görgessen lefelé, majd kattintson az "Azure CDN".
    
    A **Azure CDN** panel jelenik meg.

    ![CDN engedélyezése navigációs][cdn-enable-navigation]
    
2. Írja be a szükséges adatokat az új végpont létrehozásához
    - **CDN-profil**: hozzon létre egy új, vagy egy meglévő profil.
    - **IP-címek**: csak akkor kell új CDN-profil létrehozásakor válassza ki a tarifacsomagot.
    - **CDN-végpont nevének**: Adja meg a választott / egy végpont nevét.

    > [!TIP]
    > A létrehozott CDN-végpont használja az állomásnevet a tárfiók származási alapértelmezés szerint.

    ! [cdn új végpont létrehozásához] [a cdn-új-végpont-létrehozása]

3. A létrehozás után az új végpont a fenti végpont listában jelennek meg.

    ![tárolási új CDN-végpont][cdn-storage-new-endpoint]

> [!NOTE]
> Azure CDN-bővítmény engedélyezése a CDN is választhatja. [Oktatóanyag](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>3. lépés: További CDN-funkciók engedélyezése

A storage-fiók "Azure CDN" panelen kattintson a CDN-végpont a listából a CDN konfigurációs panel megnyitásához. További CDN szolgáltatásai engedélyezheti a kézbesítésre, például a tömörítés, lekérdezési karakterláncot, földrajzi szűrést. Adja hozzá az egyéni tartomány leképezése a CDN-végpont is, és az egyéni tartomány HTTPS engedélyezése.
    
![CDN cdn tárolással][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>4. lépés: Hozzáférési CDN-tartalom
A CDN a gyorsítótárazott tartalom eléréséhez a CDN a portálon megadott URL-CÍMÉT használja. A gyorsítótárazott blob címet a következőhöz hasonló lesz:

http://<*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*Blobnév*\>

> [!NOTE]
> Ha engedélyezi a CDN hozzáférést egy tárfiókba, az összes nyilvánosan elérhető objektumok jogosultak a CDN peremhálózati gyorsítótár. Ha módosítja a CDN jelenleg gyorsítótárazott objektumhoz, az új tartalom nem lesz elérhető a CDN mindaddig, amíg a CDN tartalmát frissíti, a gyorsítótárazott tartalom idő a működés közbeni időszak lejártával.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>5. lépés: A tartalom eltávolítása a CDN-t
Ha már nem szeretne gyorsítótárazása az objektum az az Azure Content Delivery Network (CDN), akkor is igénybe vehet az alábbi lépések egyikét:

* Biztosíthatja, hogy a tároló privát nyilvános helyett. További információk: [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).
* Tiltsa le, vagy törölni a CDN-végpontot a felügyeleti portál használatával.
* Az üzemeltetett szolgáltatás nem válaszol a kérelmekre a objektum módosíthatja.

Az objektum már gyorsítótárazza a CDN gyorsítótárában marad, amíg az objektum idő a működés közbeni időszakának lejártáig támogatja, vagy a végpont véglegesen törlődnek. Az idő a működés közbeni időszak lejártával a CDN ellenőrzi, hogy a CDN-végpont továbbra is érvényes, és az objektum névtelenül továbbra is elérhetők maradnak. Ha nem, majd az objektum rendszer már nem gyorsítótárazható.

## <a name="additional-resources"></a>További források
* [CDN-tartalom leképezése egyéni tartományra](cdn-map-content-to-custom-domain.md)
* [Az egyéni tartomány HTTPS engedélyezése](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
