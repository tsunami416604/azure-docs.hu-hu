---
title: Service Fabric felügyelt identitás áttekintése | Microsoft Docs
description: Ez a cikk a felügyelt identitás áttekintését ismerteti.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: e2f05ba28109a9b3c88d80eb218bf94014f0f082
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243026"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Felügyelt identitás Service Fabric alkalmazáshoz (előzetes verzió)

A felhőalapú alkalmazások készítése során általános kihívást jelenti a hitelesítő adatok a kódban való kezelése, amelyekkel az alkalmazás magát a felhőalapú szolgáltatásokban hitelesíti. A hitelesítő adatok biztonságossá tétele fontos feladat, mivel soha nem jelennek meg a fejlesztői munkaállomásokon, és nincsenek bejelölve a verziókövetés számára. A Azure Active Directory (Azure AD) Azure-erőforrásainak felügyelt identitás funkciója megoldja ezt a problémát. A szolgáltatás automatikusan felügyelt identitást biztosít az Azure-szolgáltatások számára az Azure AD-ben. Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatok a kódban szerepelnének.

Az Azure-erőforrások felügyelt identitás funkciója ingyenes az Azure AD-vel az Azure-előfizetésekhez. Nincs további díj.

> [!NOTE]
> Az Azure-erőforrások felügyelt identitása a korábban Managed Service Identity (MSI) néven ismert szolgáltatás új neve.

## <a name="terminology"></a>Szakkifejezések

A felügyelt identitás az Azure-erőforrások dokumentációs készletében a következő kifejezéseket használja:

- **Ügyfél-azonosító** – az Azure ad által generált egyedi azonosító, amely egy alkalmazáshoz és egyszerű szolgáltatáshoz van kötve a kezdeti kiépítés során (lásd az [alkalmazás azonosítóját](/azure/active-directory/develop/developer-glossary#application-id-client-id)is).

- **Elsődleges azonosító** – a felügyelt identitáshoz tartozó egyszerű szolgáltatásnév, amely az Azure-erőforrásokhoz való szerepköralapú hozzáférés biztosítására szolgál.

- **Egyszerű szolgáltatásnév** – egy Azure Active Directory objektum, amely egy adott bérlő HRE-alkalmazásának vetületét jelöli (az [egyszerű szolgáltatásnév](../active-directory/develop/developer-glossary.md#service-principal-object)is látható).


## <a name="about-managed-identities-in-azure"></a>A felügyelt identitások ismertetése az Azure-ban

- [A felügyelt identitás (MI) típusai az Azure-ban](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Hogyan működik a rendszer által hozzárendelt felügyelt identitás az Azure-ban](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Hogyan működik a felhasználó által definiált felügyelt identitás (MI) az Azure-ban](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Service Fabric alkalmazások támogatott forgatókönyvei

A Service Fabric felügyelt identitásai csak az Azure-ban üzembe helyezett Service Fabric-fürtökön támogatottak, és csak az Azure-erőforrásként üzembe helyezett alkalmazásokhoz. egy nem Azure-erőforrásként üzembe helyezett alkalmazás nem rendelhető hozzá identitáshoz. Elméletileg a felügyelt identitások támogatása az Azure Service Fabric-fürtben két fázisból áll:

1. Rendeljen hozzá egy vagy több felügyelt identitást az alkalmazás-erőforráshoz; egy alkalmazáshoz egyetlen rendszer által hozzárendelt identitás és/vagy 32 felhasználó által hozzárendelt identitás rendelhető hozzá.

2. Az alkalmazás definícióján belül leképezheti az alkalmazáshoz rendelt identitások egyikét az alkalmazást alkotó egyes szolgáltatásokhoz.

Egy alkalmazás rendszer által hozzárendelt identitása egyedi az adott alkalmazáshoz; a felhasználó által hozzárendelt identitás önálló erőforrás, amely több alkalmazáshoz is hozzárendelhető. Egy alkalmazásban az alkalmazás egyetlen identitása (akár rendszer által hozzárendelt vagy felhasználó által hozzárendelt) is hozzárendelhető több szolgáltatáshoz, de minden egyes szolgáltatáshoz csak egy identitás rendelhető hozzá. Végül a szolgáltatást explicit módon hozzá kell rendelni ahhoz, hogy hozzáférjenek ehhez a szolgáltatáshoz. Az alkalmazásnak az összetevő-szolgáltatásaihoz való hozzárendelése lehetővé teszi az alkalmazáson belüli elkülönítést – a szolgáltatás csak az ahhoz hozzárendelt identitást használja (és egyáltalán nem, ha nem lett explicit módon hozzárendelve egyet.)  

Az előzetes kiadáshoz támogatott forgatókönyvek listája a következő:

   - Új alkalmazás üzembe helyezése egy vagy több szolgáltatással, valamint egy vagy több hozzárendelt identitás

   - Rendeljen hozzá egy vagy több felügyelt identitást egy meglévő alkalmazáshoz az Azure-erőforrások elérése érdekében; az alkalmazást saját Azure-erőforrásként kell üzembe helyezni


A következő forgatókönyvek nem támogatottak vagy nem ajánlottak; vegye figyelembe, hogy ezeket a műveleteket nem lehet letiltani, de az alkalmazásokban kimaradást eredményezhet:

   - Egy alkalmazáshoz rendelt identitások eltávolítása vagy módosítása; Ha módosításokat kell végeznie, külön központi telepítéseket kell elküldenie, hogy először új identitás-hozzárendelést adjon hozzá, majd egy korábban hozzárendelt egyet. Az identitás meglévő alkalmazásból való eltávolítása nem kívánt hatással lehet, például nem frissíthető állapotban hagyja az alkalmazást. Biztonságosan törölheti az alkalmazást, ha szükség van az identitás eltávolítására; vegye figyelembe, hogy ez törli az alkalmazáshoz társított rendszer-hozzárendelt identitást (ha van ilyen), és eltávolítja az alkalmazáshoz rendelt felhasználó által hozzárendelt identitásokkal rendelkező társításokat.

   - A felügyelt identitások SF-támogatása jelenleg nem integrált a [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); az integrációt a felügyelt identitás funkciójának előzetes verziójának végéig fogja megvalósítani.

>
> [!NOTE]
>
> Ez a funkció előzetes verzióban érhető el. ilyen esetben előfordulhat, hogy a gyakori változások változhatnak, és előfordulhat, hogy nem alkalmasak az éles környezetekben való üzembe helyezésre.

## <a name="next-steps"></a>Következő lépések
* [Új Azure Service Fabric-fürt üzembe helyezése felügyelt identitások támogatásával](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Felügyelt identitás támogatásának engedélyezése meglévő Azure Service Fabric-fürtben](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése rendszerhez rendelt felügyelt identitással](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Azure Service Fabric-alkalmazás üzembe helyezése felhasználó által hozzárendelt felügyelt identitással](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Service Fabric alkalmazás felügyelt identitásának kihasználása a szolgáltatási kódból](./how-to-managed-identity-service-fabric-app-code.md)
* [Azure Service Fabric-alkalmazások hozzáférésének biztosítása más Azure-erőforrásokhoz](./how-to-grant-access-other-resources.md)
* [Alkalmazás-titkok deklarálása és használata KeyVaultReferences-ként](./service-fabric-keyvault-references.md) 
