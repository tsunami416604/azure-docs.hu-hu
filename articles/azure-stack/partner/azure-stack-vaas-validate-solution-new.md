---
title: Egy új Azure Stack megoldás ellenőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan érvényesítheti egy új megoldást az Azure Stack érvényesítési szolgáltatásként.
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
ms.openlocfilehash: 1e908a8cf5576ce3bc3d58d1ef6f29d596ebc58b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158177"
---
# <a name="validate-a-new-azure-stack-solution"></a>Egy új Azure Stack megoldás ellenőrzése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, hogyan használhatja a megoldás szűrőérvényesítési munkafolyamat tanúsító új Azure Stack-megoldások.

Az Azure Stack megoldás, a hardver anyagjegyzék (AJ), amelyek közösen egyeztetett a Microsofttal, és megfelelt a Windows Server embléma minősítési követelményeinek. Is használhatja a megoldás-ellenőrzési folyamat során a hardverhez, amely miatt minősül megoldást AJ változás történt *új*. Milyen lép működésbe kapcsolatos kérdések esetén egy **új** vagy **tanúsításra** forduljon a megoldás [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

A megoldás tanúsításához kétszer futtatni a munkafolyamatot. Futtassa egyszer a *minimálisan* konfiguráció támogatott. A másodszor, futtassa a *maximális* konfigurációja. A Microsoft igazolja a megoldás, ha mindkét adja át az összes teszt.

Ez a rövid útmutató a megoldás hozzáadását és a tesztek futtatása a folyamat a kezdeti időszak után is beolvassa.

## <a name="add-a-new-solution"></a>Egy új megoldás hozzáadása

1. Jelentkezzen be a [érvényesítési portál](https://azurestackvalidation.com).
2. Válassza ki **új megoldás**.
3. Adjon meg egy nevet a megoldást, és válasszon **mentése**.

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
4. Válassza ki **feltöltése** és vegye fel a telepítési konfigurációs fájlt. Ez az egy választható lépés. A teszt paramétereit is hozzáadhat a következő rész lépéseit követve.

    > [!note]  
    > Létrehozhat a konfigurációs fájlt a környezeti paraméterek a paraméterek hozzáadásával egy közös teszt paraméterek szakaszok a felületen. A fájl az Azure Stack üzemelő példányból érvényesíti a szolgáltatás által létrehozott kérheti le. Útmutatásért lásd: [szolgáltatásként az Azure Stack érvényesítési az általános munkafolyamat-paraméterek](azure-stack-vaas-parameters.md).

5. Adja hozzá a környezeti paramétereket. További információkért lásd: [adja hozzá környezeti paraméterek](#add-environmental-parameters).
6. Adja hozzá a közös teszt paramétereket. További információkért lásd: [tesztelési általános paraméterek hozzáadása](#add-common-test-parameters).

    A teszt definíció függően a teszt előfordulhat, hogy adjon meg egy értéket, függetlenül a következő általános paramétereket, vagy a közös paraméter értéke a felülírását is lehetővé teheti.

7. Kattintson a **küldés** a vizsgálat ütemezése.

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

- [Le, vagy egy tesztet](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).