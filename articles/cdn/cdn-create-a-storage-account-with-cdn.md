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
ms.date: 01/04/2018
ms.author: mazha
ms.openlocfilehash: 022071f7825cb9184bd4c815c09e1c202a0a6f91
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Azure-tárfiók integrálása az Azure CDN szolgáltatás használata
Az Azure storage Azure tartalom Delivery Network (CDN) engedélyezheti a tartalmak gyorsítótárazására való. Az Azure CDN tartalmak nagy sávszélességű kézbesítéséhez globális megoldást kínál a fejlesztők. Azt is a gyorsítótár-blobok és számítási példányokért fizikai csomópontokon az Amerikai Egyesült Államok, Európa, Ázsia, Ausztrália és Dél-Amerika statikus tartalmát.

## <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása
A következő eljárással hozzon létre egy új tárfiókot, Azure-előfizetésre. A storage-fiókok hozzáférést ad az Azure Storage szolgáltatás. A tárfiók eléréséhez szükséges az Azure Storage szolgáltatás összetevői a névtér a legmagasabb szintű jelöli: Azure Blob, Queue és Table storage. További információkért lásd: [Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md).

Hozzon létre egy tárfiókot, a szolgáltatás-rendszergazda vagy egy coadministrator a társított előfizetés kell lennie.

> [!NOTE]
> Többféle módszer segítségével hozzon létre egy tárfiókot, beleértve az Azure portál és a PowerShell. Ez az oktatóanyag bemutatja, hogyan használható az Azure-portálon.   
> 

**A storage-fiók egy Azure-előfizetés létrehozása**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal felső sarokban válassza **hozzon létre egy erőforrást**. Az a **új** ablaktáblán válassza előbb **tárolási**, majd válassza ki **tárfiók - blob, a fájl, a tábla, a várólista**.
    
    A **storage-fiók létrehozása** ablaktáblán jelenik meg.   

    ![Tárolási fiók ablaktábla létrehozása](./media/cdn-create-a-storage-account-with-cdn/cdn-create-new-storage-account.png)

3. Az a **neve** adjon meg egy altartomány nevet. Ez a bejegyzés 3-24 kisbetűket és számokat tartalmazhat.
   
    Ez az érték lesz az állomásnév az URI blob, sor vagy tábla erőforrások az előfizetés megoldására használt belül. Blob Storage tárolóban erőforrás megoldására URI használ a következő formátumban:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    Ha  *&lt;StorageAccountLabel&gt;*  a megadott érték hivatkozik a **neve** mezőbe.
   
    > [!IMPORTANT]    
    > Az URL-cím címke képezi a tárfiók URI altartomány, és az Azure-ban az összes üzemeltetett szolgáltatások egyedinek kell lennie.
   
    Ezt az értéket a tárfiók a portálon, vagy ha ezt a fiókot programokon keresztül éri el a neveként is használja.
    
4. Az alapértelmezett értéket használja **telepítési modell**, **fiók kind**, **teljesítmény**, és **replikációs**. 
    
5. A **előfizetés**, válassza ki az előfizetést a storage-fiók használata.
    
6. A **erőforráscsoport**, válassza ki vagy hozzon létre egy erőforráscsoportot. Erőforráscsoportok kapcsolatos információkért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups).
    
7. A **hely**, jelöljön ki egy helyet a tárfiók.
    
8. Kattintson a **Létrehozás** gombra. A storage-fiók létrehozásának folyamata befejezéséhez több percig is eltarthat.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>2. lépés: A tárfiók CDN engedélyezése

A tárfiók CDN engedélyezheti a tárfiók-ről. 

1. Válasszon egy tárfiókot az irányítópultról, és válasszon **Azure CDN** a bal oldali ablaktáblán. Ha a **Azure CDN** gomb nem jelennek meg azonnal, megadhatja a CDN-t a **keresési** azoknak a bal oldali ablaktáblán.
    
    A **Azure Content Delivery Network** ablaktáblán jelenik meg.

    ![CDN-végpont létrehozása](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)
    
2. Írja be a szükséges adatokat az új végpont létrehozásához:
    - **CDN-profil**: hozzon létre egy új CDN-profilt, vagy egy meglévő CDN-profil.
    - **IP-címek**: egy tarifacsomagra csak akkor, ha a CDN-profil létrehozásakor válassza ki.
    - **CDN-végpont nevének**: Adja meg a CDN-végpont nevét.

    > [!TIP]
    > Alapértelmezés szerint egy új CDN-végpont a tárfiók állomásneve használja az eredeti kiszolgálóra.

3. Kattintson a **Létrehozás** gombra. Miután létrehozta a végpontot, végpont listájában jelenik meg.

    ![Tárolási új CDN-végpont](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!NOTE]
> Ha meg szeretné határozni a CDN-végpontot, például a optimalizálási típusát speciális konfigurációs beállításait használhatja a [Azure CDN bővítmény](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) a CDN-végpontot, vagy a CDN-profil létrehozásához.

## <a name="step-3-enable-additional-cdn-features"></a>3. lépés: További CDN-funkciók engedélyezése

A tárfiók **Azure CDN** ablaktáblán válassza ki a CDN-végpont a CDN konfigurációs ablaktábla megnyitása a listából. További CDN szolgáltatásai engedélyezheti a kézbesítésre, például tömörítést, a lekérdezési karakterlánc és a földrajzi szűrést. Adja hozzá az egyéni tartomány leképezése a CDN-végpont is, és az egyéni tartomány HTTPS engedélyezése.
    
![Tárolási CDN végpont-konfiguráció](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)

## <a name="step-4-access-cdn-content"></a>4. lépés: Hozzáférési CDN-tartalom
A CDN a gyorsítótárazott tartalom eléréséhez a CDN a portálon megadott URL-CÍMÉT használja. Az a gyorsítótárba helyezett binárisobjektum-cím formátuma a következő:

http://<*EndpointName*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Miután engedélyezte a tárfiók CDN hozzáférést, az összes nyilvánosan elérhető objektumok jogosultak a CDN peremhálózati gyorsítótár. Ha módosítja a CDN jelenleg gyorsítótárazott objektumhoz, az új tartalom nem lesznek elérhetők keresztül CDN amíg nem CDN tartalmát frissíti, a gyorsítótárazott tartalom idő a működés közbeni időszak lejárta után.

## <a name="step-5-remove-content-from-the-cdn"></a>5. lépés: A tartalom eltávolítása a CDN-t
Ha már nem szeretné az Azure CDN objektumok gyorsítótárazása, hajthatók végre a következő lépésekből áll:

* Ellenőrizze a tároló magánfelhő, nyilvános helyett. További információkért lásd: [tárolók és blobok névtelen olvasási hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).
* Tiltsa le, vagy a CDN-végpont törlése az Azure-portál használatával.
* Az üzemeltetett szolgáltatás nem válaszol a kérelmekre a objektum módosítása.

Olyan objektum, amely már szerepel a gyorsítótárban Azure CDN gyorsítótárazott marad, amíg az objektum idő a működés közbeni időszakának lejártáig támogatja, vagy a végpont véglegesen törlődnek. Az idő a működés közbeni időszak lejártával Azure CDN ellenőrzi, hogy a CDN-végpont továbbra is érvényes, és az objektum névtelenül továbbra is elérhetők maradnak. Ha nem, az objektum már nem gyorsítótárazható.

## <a name="additional-resources"></a>További források
* [Egyéni tartomány hozzáadása a CDN-végponthoz](cdn-map-content-to-custom-domain.md)
* [HTTPS egyéni Azure CDN-tartomány konfigurálása](cdn-custom-ssl.md)

