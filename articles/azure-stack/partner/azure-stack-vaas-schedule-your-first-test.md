---
title: Használja az érvényesítés szolgáltatását az Azure Stack portálon az első vizsgálat ütemezése |} A Microsoft Docs
description: Az Azure Stack portal szolgáltatásként az érvényesítés használatával az első vizsgálat ütemezése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0f681070df4b4b3384171c05edb3851abec2ab5c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235411"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Gyors útmutató: Használja az érvényesítés egy portált az első vizsgálat ütemezése

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, az ellenőrzést, mint egy (VaaS) portált a hardver ellenőrzése a az első vizsgálat ütemezéséről. A helyi ügynök kell üzembe helyezni az Azure Stack megoldás ellenőrző tesztek futtatása előtt kell kiértékelni.

Ebben a rövid, hozzáadja a megoldás és tesztek futtatásához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt Ez a rövid útmutató követéséhez kell rendelkeznie:
 - Egy érvényesítési, mint egy szolgáltatásfiókra. Útmutatásért lásd: [beállítása az érvényesítés, amely szolgáltatásfiókként](azure-stack-vaas-set-up-account.md).  
- A rendszerre telepített helyi ügynök. Útmutatásért lásd: [a helyi ügynök és a teszt virtuális gépek üzembe helyezése](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Egy új megoldás hozzáadása

1. Jelentkezzen be a [érvényesítési portál](https://azurestackvalidation.com).

    ![Jelentkezzen be az érvényesítési Portalra](media/vaas_portalsignin.png)  

2. Válassza ki **új megoldás**.
3. Adja meg a megoldás nevét, és válassza ki **mentése**.

## <a name="create-a-solution-validation-workflow"></a>A megoldás szűrőérvényesítési munkafolyamat létrehozása

1. Válassza ki a megoldás nevét.
2. Válassza ki **kezelés** a a **megoldás ellenőrzések** csempére.

    ![Megoldás-ellenőrzések](media/image2.png)

## <a name="create-a-solution-workflow"></a>Megoldás munkafolyamat létrehozása

1. Válassza ki **új megoldás érvényesítési**.
2. Írja be a az ellenőrzést.
3. Válassza ki **minimális** vagy **maximális**.  
    - **Minimum**  
    A megoldás úgy van konfigurálva, a csomópontok minimális támogatott számát.  
    - **Maximális**  
    A megoldás van konfigurálva, a csomópontok támogatott maximális számát.
4. Adja hozzá a környezeti paramétereket. További információkért lásd: [adja hozzá környezeti paraméterek](#add-environmental-parameters).
5. Adja hozzá a közös teszt paramétereket. További információkért lásd: [tesztelési általános paraméterek hozzáadása](#add-common-test-parameters).

    A teszt definíció függően a teszt előfordulhat, hogy adjon meg egy értéket, függetlenül a következő általános paramétereket, vagy a közös paraméter értéke a felülírását is lehetővé teheti.
6. Kattintson a **küldés** a vizsgálat ütemezése.

## <a name="add-environmental-parameters"></a>Környezeti paraméterek hozzáadása

Adja hozzá a következő környezeti paraméterek:

| Teszt pass-információk | Szükséges | Leírás |
| --- | --- | --- | --- |
| Az Azure Stack-build | Szükséges | Az Azure Stack-build (például 20170501.1) számértéknek kell lennie egy érvényes Azure Stack számát vagy a verziója, például 1.0.170330.9 összeállítása |
| Bérlőazonosító | Szükséges | Az Active Directory-bérlő azonosítója. Ez lehet egy GUID Azonosítót (például ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Régió | Szükséges | Az Azure Stack üzembe helyezési régióhoz |
| Bérlő Resource Manager-végpont | Szükséges | Végpont (például a bérlő Azure Resource Manager műveletekhez https://management.loc-ext.domain.com) |
| Rendszergazdai Resource Manager-végpont | Nem szükséges | Végpont (például a bérlő Azure Resource Manager műveletekhez https://management.loc-ext.domain.com) |
| Külső teljes Tartományneve | Nem szükséges | Külső teljesen minősített tartománynév végpontok utótagként használni. (például local.azurestack.external vagy redmond.contoso.com) |
| Csomópontok száma | Szükséges | A megoldás a csomópontok számát. |

## <a name="add-common-test-parameters"></a>Általános tesztelési paraméterek hozzáadása

Adja hozzá a következő gyakori teszt paraméterekkel:

| Teszt pass-információk | Szükséges | Leírás |
| --- | --- | --- |
| Bérlő felhasználóneve | Szükséges | Bérlő felhasználóneve (például tenant@contoso.onmicrosoft.com) |
| Bérlő jelszó | Szükséges | A bérlő jelszava. |
| Szolgáltatás-rendszergazda felhasználóneve | Nem szükséges | Bérlő felhasználóneve (például tenant@contoso.onmicrosoft.com) |
| Szolgáltatás-rendszergazda jelszava | Nem szükséges | Szolgáltatás-rendszergazda felhasználóneve (például serviceadmin@contoso.onmicrosoft.com) |
| Felhőalapú rendszergazdai felhasználónév | Nem szükséges | Az Azure Stack tartományi rendszergazdai fiókot (például contoso\cloudadmin) |
| Felhőalapú rendszergazdai jelszó | Nem szükséges | |
|  Diagnosztikai kapcsolati karakterlánc | Nem szükséges | SAS URI-t egy Azure Storage-fiókot, mely a diagnosztikai naplók lesznek másolva a teszt végrehajtása során. Lásd: [hozzon létre egy Azure storage-blobba naplók tárolására](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>Értékét a **diagnosztikai kapcsolati karakterlánc** általános paraméter tárolja a szolgáltatás és az összes teszt a munkafolyamatban, amely használja ezt a paramétert adja meg az időt adja meg. Ha az SAS URL-cím lejárati számított 30 napon belül, egy új SAS URL-címet a gyakori paraméterek oldalon kell adnia. |
| Címke - név | Nem szükséges |  Leíró címkék címkével ellátni a munkafolyamat adható meg. Ez a címke nevét. |
| Címke - Érték | Nem szükséges | Leíró címkék címkével ellátni a munkafolyamat adható meg. Ez a címke értéke. |

## <a name="next-steps"></a>További lépések

- [Egy új Azure Stack megoldás ellenőrzése](azure-stack-vaas-validate-solution-new.md)  
- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).
