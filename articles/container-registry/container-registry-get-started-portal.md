---
title: "Privát Docker-beállításjegyzék létrehozása – Azure Portal | Microsoft Docs"
description: "Bevezetés a privát Docker-beállításjegyzékek létrehozásába és kezelésébe az Azure Portalon"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 33944d34ce695e1729b761a7f762e24a6dce70a2
ms.lasthandoff: 03/06/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Privát Docker-tárolójegyzék létrehozása az Azure Portalon
Az Azure Portalon létrehozhat egy tároló-beállításjegyzéket, és kezelheti a beállításait. A tároló-beállításjegyzékeket létrehozhatja és kezelheti az [Azure CLI 2.0 parancsaival](container-registry-get-started-azure-cli.md) vagy programozott módon a tároló-beállításjegyzék [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=834376) is.

Háttér-információkért és a fogalmakkal kapcsolatban lásd [az áttekintést](container-registry-intro.md).


> [!NOTE]
> A Container Registry jelenleg előzetes verzióban érhető el.


## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
1. A [portálon](https://portal.azure.com) kattintson a **+Új** elemre.
2. Keressen a piactéren **tároló-beállításjegyzékeket**.
3. Válassza a **Container Registry (előzetes verzió)** elemet, amelynek közzétevője a **Microsoft**. 
    ![Container Registry szolgáltatás az Azure Marketplace-en](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Kattintson a **Létrehozás** gombra. Megjelenik a **Tároló-beállításjegyzék** panel.

    ![A tároló-beállításjegyzékek beállításai](./media/container-registry-get-started-portal/container-registry-settings.png)
5. A **Tároló-beállításjegyzék** panelen adja meg a következő információkat. Amikor elkészült, kattintson a **Létrehozás** gombra.
   
    a. **Beállításjegyzék neve**: egy globálisan egyedi legfelső szintű tartománynév az adott beállításjegyzékhez. Ebben a példában a beállításjegyzék neve *myRegistry01*, de helyettesítsen be egy saját, egyedi nevet. A név csak betűket és számokat tartalmazhat.
   
    b. **Erőforráscsoport**: Válasszon egy meglévő [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
   
    c. **Hely**: Válasszon ki egy Azure-adatközpontot, ahol a szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/), például az **USA déli középső régióját**. 
   
    d. **Rendszergazdai felhasználó**: Ha szeretné, engedélyezze egy rendszergazdai felhasználó számára a beállításjegyzék elérését. Ezt a beállítást a beállításjegyzék létrehozását követően módosíthatja.
   
    > [!IMPORTANT]
    > A tároló-beállításjegyzékek, amellett, hogy hozzáférést biztosítanak egy rendszergazdai felhasználói fiókon keresztül, támogatják az Azure Active Directory egyszerű szolgáltatásaira épülő hitelesítést. További információkat és szempontokat [a tároló-beállításjegyzékkel való hitelesítéssel kapcsolatos cikkben](container-registry-authentication.md) találhat.
    >
    
    e. **Storage-fiók**: Hozzon létre egy [Storage-fiókot](../storage/storage-introduction.md) az alapértelmezett beállítással, vagy válasszon egy meglévő tárfiókot ugyanezen a helyen. A Premium Storage jelenleg nem támogatott.


## <a name="manage-registry-settings"></a>Beállításjegyzék beállításainak kezelése
A beállításjegyzék létrehozását követően a beállításjegyzék-beállításokat a portál **Tároló-beállításjegyzékek** paneljéről kiindulva találja meg. Például szüksége lehet a beállításjegyzékbe való bejelentkezés beállításaira, vagy esetleg szeretné engedélyezni vagy letiltani a rendszergazdai felhasználót.

1. A **Tároló-beállításjegyzékek** panelen kattintson a beállításjegyzék nevére.
   
    ![Tároló-beállításjegyzék panel](./media/container-registry-get-started-portal/container-registry-blade.png)
2. A hozzáférési beállítások kezeléséhez kattintson a **Hozzáférési kulcs** elemre.
   
    ![Hozzáférés a tároló-beállításjegyzékhez](./media/container-registry-get-started-portal/container-registry-access.png)
3. Ügyeljen a következő beállításokra:
   
   * **Bejelentkezési kiszolgáló** – A teljes név, amelyet a beállításjegyzékbe való bejelentkezéshez használ. Ebben a példában ez `myregistry01-contoso.azurecr.io`.
   * **Rendszergazdai felhasználó** – Ki- és bekapcsolhatja a beállításjegyzék rendszergazdai felhasználói fiókját.
   * **Felhasználó** és **Jelszó** – A rendszergazdai felhasználói fiók (ha engedélyezve van) hitelesítő adatai, amelyek használatával bejelentkezhet a beállításjegyzékbe. Szükség esetén újból létrehozhatja a jelszót.

## <a name="next-steps"></a>Következő lépések
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)




