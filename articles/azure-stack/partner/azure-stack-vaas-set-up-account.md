---
title: Állítsa be az Azure Stack érvényesítési, mint egy szolgáltatásfiókra |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be, amely szolgáltatásfiókként az érvényesítés.
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
ms.openlocfilehash: 3f87721dcf7485e15a473e82597f6bf4baeca659
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "42139560"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Az ellenőrzés, amely szolgáltatásfiókként beállítása

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

(VaaS) szolgáltatás ellenőrzése egy Azure-szolgáltatás, amely a Microsoft Azure Stack-partnereknek, akik egy közös mérnöki szerződések tervezéséhez, fejlesztéséhez, ellenőrzése, értékesítésére, üzembe helyezése és támogatja az Azure Stack megoldásokat a piacon elérhető legyen elérhető.

Ismerje meg, hogyan kérhet a rendszer készen áll a szolgáltatás érvényesítése. Állítsa be az Azure Active Directory-példányból, és egyéb szükséges feladatok végrehajtásához az első VaaS használatra kész. 

A feladatok a következők:

- Hozzon létre egy Azure Storage-blobba naplók tárolására
- A helyi ügynök telepítése
- Töltse le a rendszerképet virtuális tesztgépek tesztelését az Azure Stack-példányon

## <a name="create-an-azure-active-directory-tenant-id"></a>Hozzon létre egy Azure Active Directory-bérlő azonosítója

1. Hozzon létre egy Azure Active Directory-bérlő a a [az Azure portal](https://portal.azure.com) vagy használjon egy meglévő bérlőt.

    Javasoljuk, hogy létrehoz egy bérlőt kifejezetten a VaaS egy leíró nevet, például ContosoVaaS@onmicrosoft.com. A szerepköralapú hozzáférés-vezérlés (RBAC) funkcióját a bérlő kezelésére is, akik a fiókpartner-szervezet VaaS használni a partner által lesz használható.  
    
    További információkért lásd: [kezelése az Azure AD-címtár](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Új Azure Active Directory-bérlők létrehozásával kapcsolatos további információkért lásd: [első lépései az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Adja hozzá a szervezet azon tagjai, akik felelősek a bérlők a szolgáltatás használatával. Rendelje hozzá a következő szerepkörök egyikét a bérlő szabályozhatja a hozzáférési szintet számukra VaaS minden egyes felhasználója:

    | Szerepkör neve | Leírás |
    |---------------------|------------------------------------------|
    | Tulajdonos | Az összes erőforrás teljes hozzáféréssel rendelkezik. |
    | Olvasó | Az összes erőforrás megtekintését, de nem szerkesztheti. |
    | Teszt Közreműködője | Test-erőforrások is kezelhetők. |
    | Katalógus-közreműködő | Megoldás közzétételi erőforrások is kezelhetők. |

## <a name="set-up-your-tenant"></a>Állítsa be a bérlőjébe

Állítsa be a bérlő által a **Azure Stack érvényesítési Service** alkalmazás. 

1. A bérlő a következő információkat küldeni a Microsoftnak, vaashelp@microsoft.com.

    | Adatok | Leírás |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Szervezet neve | Hivatalos szervezet neve. |
    | Az Azure AD-bérlő könyvtár neve | Az Azure AD-bérlő címtárát neve alatt álló. |
    | Az Azure AD címtár azonosítója | Az Azure AD-bérlő Directory, a címtárhoz társított GUID.<br> Információk megkeresése az Azure AD címtár azonosítója tekintheti meg, lásd: "[Bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. Az Azure Stack fejlesztőcsapatának megerősítése, hogy a bérlő használja az VaaS portal biztosít.

3. A bérlő globális rendszergazdai hitelesítő adatainak használatával jelentkezzen be a [VaaS portál](https://azurestackvalidation.com/
). Válassza ki **fiókom**.

    ![Jelentkezzen a VaaS portálra](media/vaas_portalsignin.png)

3. A hely a rendszer kéri, hogy a VaaS hozzáférést. Fogadja el a feltételeket, a folytatáshoz.

## <a name="assign-user-roles"></a>Felhasználói szerepkörök hozzárendelése

Felhasználói szerepkör hozzárendelése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **Azure Active Directory** a a **identitás** csoport.
3. Válassza ki **vállalati alkalmazások** > **Azure Stack érvényesítési Service** alkalmazás.
4. Válassza a **Felhasználók és csoportok** elemet. A **Azure Stack érvényesítési Service – felhasználók és csoportok** panel felsorolja a felhasználók számára a engedélyt az alkalmazás használatára.
5. Válassza ki **+ Hozzáadás felhasználó** , vegyen fel egy hozzárendelést.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Hozzon létre egy Azure storage-blobba naplók tárolására

VaaS diagnosztikai naplókat hoz létre, az ellenőrzőtesztek futtatásakor. Egy Azure blob service SAS URL-címet, a naplók tárolásához szüksége lesz egy olyan helyre. A storage-fiók is használható a tároló az OEM testreszabási csomagokat.

Ezek a lépések bemutatják, hogyan állíthatja be, és hozzon létre egy storage--szolgáltatásként (SAS) URI az Azure storage-fiókhoz tartozó, valamint a helyét adja meg a tárfiók a VaaS portálon tesztek a portál indításakor.

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

1. Hozzon létre egy tárfiókot, kövesse az utasításokat, [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md).

2. A tárfiók típusát kiválasztásakor válassza ki a **a Blob storage-** fiók típusa.

### <a name="generate-a-sas-url-for-the-storage-account"></a>A tárfiók SAS URL létrehozása

1. Keresse meg a fent létrehozott tárfiókot.

2. A panel a **beállítások**válassza **közös hozzáférésű jogosultságkód**.

3. Csak ellenőrizze **Blob** a **engedélyezett szolgáltatások beállítások** (törölje a jelet a fennmaradó).

4. Ellenőrizze **szolgáltatás**, ** tárolót, és **objektum** a **engedélyezett erőforrástípusok**.

5. Ellenőrizze **olvasási**, **írási**, **lista**, **Hozzáadás**, **létrehozás** a **engedélyezett jogosultságok**  (törölje a jelet a fennmaradó).

6. Állítsa be **kezdési idő** az aktuális idő és **befejezési idő** három hónappal az aktuális időponthoz képest.

7. Válassza ki **készítése SAS és kapcsolati karakterlánc** , és mentse a **Blob service SAS URL-címet** karakterlánc.

> [!Note]  
> Az SAS URL-címet beállítani, ha az URL-cím jött létre a Befejezés időpontjában lejár. Győződjön meg arról, hogy az URL-cím elég érvényes előtt megosztja azokat a termékért felelős csoportja hibakeresése a, vagy hogy az URL-cím érvényes-e több mint 30 napig tesztek ütemezése.

## <a name="next-steps"></a>További lépések

- Használja a VaaS helyi ügynök a hardver ellenőrzése. További útmutatást lásd: [a helyi ügynök és a teszt virtuális gépek üzembe helyezése](azure-stack-vaas-test-vm.md).
- Tudjon meg többet a [szolgáltatásként az Azure Stack érvényesítési](https://docs.microsoft.com/azure/azure-stack/partner).