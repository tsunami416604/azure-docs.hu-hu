---
title: "Tároló-beállításjegyzék létrehozása a portálon | Microsoft Docs"
description: "Bevezetés az Azure Container Registryk létrehozásába és kezelésébe az Azure Portalon"
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
translationtype: Human Translation
ms.sourcegitcommit: aa4b960ed75b5a4702317bf557b4588e7a54fa0e
ms.openlocfilehash: c22fee1a9172eba28d8f841d973704934cdb3ebb

---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Tároló-beállításjegyzék létrehozása az Azure Portalon
Az Azure Portalon létrehozhat egy tároló-beállításjegyzéket, és kezelheti a beállításait. A tároló-beállításjegyzékeket létrehozhatja és kezelheti az [Azure CLI 2.0 előzetes verziója parancsaival](container-registry-get-started-azure-cli.md) vagy programozott módon a tároló-beállításjegyzék [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=834376) is.

Háttérinformációkért és a fogalmakkal kapcsolatban lásd: [Mi az az Azure Container Registry?](container-registry-intro.md)


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
   
    a. **Beállításjegyzék neve** – egy globálisan egyedi legfelső szintű tartománynév az adott beállításjegyzékhez. Ebben a példában a beállításjegyzék neve *myRegistry01*, de helyettesítsen be egy saját, egyedi nevet. A név csak betűket és számokat tartalmazhat.
   
    b. **Erőforráscsoport** – Válasszon egy meglévő [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét. 
   
    c. **Hely** – Válasszon ki egy Azure-adatközpontot, ahol a szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/), például az **USA déli középső régióját**. 
   
    d. **Rendszergazdai felhasználó** – Ha szeretné, engedélyezze egy rendszergazdai felhasználó számára a beállításjegyzék elérését. Ezt a beállítást a beállításjegyzék létrehozását követően módosíthatja.
   
   > [!IMPORTANT]
   > A tároló-beállításjegyzékek, amellett, hogy hozzáférést biztosítanak egy rendszergazdai felhasználói fiókon keresztül, támogatják az Azure Active Directory egyszerű szolgáltatásaira épülő hitelesítést. További információkat és szempontokat [a tároló-beállításjegyzékkel való hitelesítéssel kapcsolatos cikkben](container-registry-authentication.md) találhat.
   
    e. **Storage-fiók** – Hozzon létre egy [Storage-fiókot](../storage/storage-introduction.md) az alapértelmezett beállítással, vagy válasszon egy meglévő tárfiókot ugyanezen a helyen.

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






<!--HONumber=Nov16_HO3-->


