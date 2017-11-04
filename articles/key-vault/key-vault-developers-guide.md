---
title: "Az Azure Key Vault fejlesztői útmutatója"
description: "A fejlesztők az Azure Key Vault segítségével kezelheti a kriptográfiai kulcsok a Microsoft Azure-környezeten belül."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: bruceper
ms.openlocfilehash: 8d617726a4ee9335728ab82104efbd845e3b0d05
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi, hogy biztonságosan férjenek hozzá a bizalmas adatok az alkalmazások belül:

- Kulcsok és titkos védettek, anélkül, hogy a kód írását, saját magának, és könnyen képes használni azokat az alkalmazásokat.
- Megtörténik az ügyfelek saját rendelkezik, és összpontosíthat biztosítása a core szoftverfunkciók a saját kulcsaikat kezelését. Így az alkalmazások lesz nem tulajdonosa a felelősséget és esetleges felelősségre vonást az ügyfelek bérlői kulcsok és titkos kulcsok.
- Az alkalmazás kulcsok aláírására használható, és a titkosítási még tartja a kulcskezelést külső az alkalmazásról, így a-megoldás felel meg a földrajzilag elosztott alkalmazások.
- A Key Vault 2016 szeptemberétől kiadás, az alkalmazások használhatják a Key Vault [tanúsítványok](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). További információkért lásd: [kulcsokat, a titkos kulcsok és a tanúsítványok](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Az Azure Key Vault további általános információkért lásd: [Mi az Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Nyilvános előzetes verziójú funkciók

Rendszeres időközönként egy új Key Vault szolgáltatás nyilvános előzetes kiadás azt. Próbálja ki ezeket, és ossza meg velünk mit gondol keresztül azurekeyvault@microsoft.com, a visszajelzési e-mail címet.

### <a name="storage-account-keys---july-10-2017"></a>Tárfiókkulcsok - 2017. július 10.

>[!NOTE]
>A frissítés csak az Azure Key Vault a **Tárfiókkulcsok** szolgáltatás egyelőre.

Ebben az előzetes verzióban az új Tárfiókkulcsok szolgáltatást tartalmaz, ezek kapcsolódási; keresztül érhető el [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) és [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Az új Tárfiókkulcsok szolgáltatásról további információkért lásd: [Azure Key Vault tárolási fiók kulcsok áttekintése](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videók

Ez a videó bemutatja, hogyan saját kulcstároló létrehozása és a "Hello Key Vault" mintaalkalmazást a használatával.

- [Key Vault fejlesztői – gyors üzembe helyezési útmutató](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

A fenti videó erőforrások:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Az Azure Key Vault mintakód](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Hozza létre és kezelje a kulcstárolók

Az Azure Key Vault biztonságosan tárolni a hitelesítő adatokat és egyéb kulcsok és titkos lehetőséget biztosít, de a kódot kell hitelesítenie magát a Key Vault kérheti le azokat. Felügyelt szolgáltatás identitásának (MSI) teszi egyszerűbbé válik a probléma megoldásához adjon az Azure-szolgáltatások automatikusan felügyelt identitást az Azure Active Directory (Azure AD). Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, többek között a Key Vault, anélkül, hogy a hitelesítő adatok a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Az MSI további információkért lásd: [felügyelt szolgáltatás identitásának (MSI) az Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/msi-overview).

Az aad-ben munkáról bővebben lásd: [alkalmazások integrálása az Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Mielőtt elkezdene dolgozni a kulcsokat, titkos kulcsokat vagy tanúsítványokat key vaultban lévő, akkor hozhat létre, és kezelheti a kulcstartót CLI-t, a PowerShell, a Resource Manager-sablonok vagy a REST, a következő cikkekben ismertetett módon:

- [Létrehozása és kezelése a parancssori felület kulcs tárolók](key-vault-manage-with-cli2.md)
- [Létrehozása és kezelése a PowerShell-lel kulcs tárolók](key-vault-get-started.md)
- [Hozzon létre egy kulcstartót, és adja hozzá a titkos kulcs Azure Resource Manager-sablonok segítségével](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Hozzon létre és többi kulcs tárolók kezelése](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>A kulcstároló kódolása

A Key Vault felügyeleti rendszer t használó programozók számára több felület áll. Ez a szakasz az összes nyelven, valamint néhány kódot exampls mutató hivatkozásokat tartalmaz. 

### <a name="supported-programming-and-scripting-languages"></a>Támogatott programozási, és programozási nyelvek

#### <a name="rest"></a>REST

A Key Vault-erőforrások összes; REST-felületen keresztül érhető el tárolók, kulcsok, a titkos kulcsok, stb. 

[Kulcs tárolói REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[.NET API-es hivatkozási Key vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

A .NET SDK 2.x verzióján további információkért lásd: a [kibocsátási megjegyzéseket](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK kulcstároló](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

A node.js a Key Vault API és a Key Vault objektum API külön. A következő cikk áttekintése egyaránt biztosít hozzáférést. 

[A Node.js az Azure Key Vault-modulok](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Python Azure Key Vault-könyvtárakban](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Az Azure CLI 2

[A kulcstároló Azure parancssori felület](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[A kulcstároló Azure PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Rövid útmutatók

- [Kulcstároló létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Ismerkedés a Key Vault a Node.js-ben](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Kódpéldák

Key Vault használatával az alkalmazások teljes példákért lásd:

- [Az Azure Key Vault-Kódminták](http://www.microsoft.com/download/details.aspx?id=45343) -.NET mintaalkalmazás *HelloKeyVault* és az Azure web service példa. 
- [Használja az Azure Key Vault-webalkalmazások](key-vault-use-from-web-application.md) -oktatóanyag segítséget nyújt az Azure Key Vault használata egy webalkalmazás az Azure-ban. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladatspecifikus útmutatás nyújtása a működik-e az Azure Key Vault:

- [Kulcstároló Bérlőazonosító módosítása után előfizetés áthelyezése](key-vault-subscription-move-fix.md) – amelyet átvisz az Azure-előfizetéshez A bérlő bérlői B, ha a meglévő kulcstárolójának érhetők el a tag (a felhasználók és az alkalmazások) bérlőben b javítás Ez az útmutató alapján.
- [Tűzfal mögött található kulcstároló eléréséhez](key-vault-access-behind-firewall.md) – a kulcstároló ügyfélalkalmazás képesnek kell lennie a különböző funkciók több végpontjainak eléréséhez kulcstároló eléréséhez.
- [Létrehozás és Transfer HSM-Protected kulcsok Azure Key vault](key-vault-hsm-protected-keys.md) – Ez segít a tervezése. létrehozni, majd a saját HSM által védett kulcsok használata az Azure Key Vault át.
- [Továbbítása biztonságos értékeket (például a jelszavak) üzembe helyezése során](../azure-resource-manager/resource-manager-keyvault-parameter.md) – kell átadni (például jelszót) biztonságos értéket paraméterként telepítésekor, ha az érték, egy Azure Key Vault a titkos kulcs tárolása, és az egyéb erőforrásokat értékre hivatkoznak Manager-sablonok.
- [Az SQL Server a bővíthető kulcskezelési Key Vault használatával](https://msdn.microsoft.com/library/dn198405.aspx) -az SQL Server-összekötő az Azure Key Vault lehetővé teszi, hogy az SQL Server és SQL-az-a-VM kihasználhatják az Azure Key Vault szolgáltatás védelmére bővíthető Key Management (EKM) szolgáltatóként az titkosítási kulcsok alkalmazások hivatkozás; Átlátható adattitkosítás, a biztonsági másolat titkosításához és oszlop a blokkszintű titkosítás.
- [Tanúsítványok telepítése a virtuális gépeknek a Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – egy felhőben futó alkalmazások egy virtuális gép Azure igényeknek megfelelően egy tanúsítványt. Hogyan meg be ezt a tanúsítványt a virtuális gép ma?
- [Hogyan állítsa be a Key Vault végpontok közötti fő elforgatás és naplózási](key-vault-key-rotation-log-monitoring.md) - e végigvezeti a kulcs Elforgatás beállításával, és az Azure Key Vault naplózását.
- [Azure webalkalmazás tanúsítványának keresztül Key Vault üzembe helyezése]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) részletes útmutatást ad a Key Vault részeként tárolja tanúsítványait [App szolgáltatástanúsítvány](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) kínál.
- [Számos alkalmazás kulcstároló elérésére engedélyt](key-vault-group-permissions-for-apps.md) Key Vault hozzáférés-vezérlési házirend csak a 16 bejegyzések támogatja. Azonban létrehozhat egy Azure Active Directory biztonsági csoport. A társított szolgáltatás rendszerbiztonsági tagok hozzáadása ehhez a biztonsági csoporthoz, és majd a hozzáférési jogot a Key Vault biztonsági csoport.
- További tevékenység-specifikus útmutatást integrálása és az Azure Key tárolók használatával, lásd: [Ryan János Azure Resource Manager sablon példákat Key vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Key Vault soft-törlés használata CLI](key-vault-soft-delete-cli.md) végigvezeti használatát és kulcstároló és különféle kulcstároló objektumok életciklusát a soft-törlés engedélyezve van.
- [Key Vault soft-törlés használata PowerShell](key-vault-soft-delete-powershell.md) végigvezeti használatát és kulcstároló és különféle kulcstároló objektumok életciklusát a soft-törlés engedélyezve van.

## <a name="integrated-with-key-vault"></a>Kulcstároló integrálva

Ezek a cikkek más forgatókönyvek és a szolgáltatások, melyek a Key Vault integrálása készül.

- [Az Azure Disk Encryption](../security/azure-security-disk-encryption.md) használja. Ez az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows-szolgáltatás és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkció a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít. A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és kezelheti a lemez titkosítási kulcsok és titkos kulcsokat a kulcstartót az előfizetést, biztosítva, hogy a virtuális gépek lemezeit az összes adat titkosítva legyenek-e az Azure tárolás közben.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) lehetőséget biztosít a fiókban tárolt adatok titkosítása számára. Kulcskezelés a Data Lake Store két üzemmódot biztosít a fő titkosítási kulcsok (MEKs), amelyek szükségesek a Data Lake Store-ban tárolt adatokat visszafejtése kezeléséhez. Vagy engedélyezheti a Data Lake Store a MEKs kezeléséhez, vagy válassza a tartsa meg az Azure Key Vault fiókkal MEKs tulajdonjogát. Kulcskezelés módját Data Lake Store-fiók létrehozásakor adja meg. 
- [Az Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) lehetővé teszi a kezelő a saját bérlőkulcsát. Például helyett a Microsoft a bérlőkulcsát (alapértelmezett) kezelése, kezelheti a szervezetére vonatkozó speciális szabályozásoknak ahhoz, hogy a saját bérlőkulcsát. A saját bérlőkulcs felügyeletéről van is néven a saját kulcs használata avagy BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault áttekintése és fogalmak

- [Key Vault soft-törlési viselkedés](key-vault-ovw-soft-delete.md) lehetővé teszi, hogy a törölt objektumok helyreállítási ismerteti, hogy a törlés véletlen vagy szándékos volt.
- [Sávszélesség-szabályozás Key Vault ügyfél](key-vault-ovw-throttling.md) megismerkedhet csomagjainkkal, a sávszélesség-szabályozás alapvető fogalmait, valamint az alkalmazás egy módszert kínál.
- [Key Vault tárolási fiók kulcsok áttekintése](key-vault-ovw-storage-keys.md) a Key Vault-integráció Azure Storage-fiókok kulcsok ismerteti.
- [Key Vault biztonsági világot](key-vault-ovw-security-worlds.md) régiók és biztonsági területeken kapcsolatának ismerteti.

## <a name="social"></a>Közösségi tartalom

- [Kulcstároló Blog](http://aka.ms/kvblog)
- [Kulcstároló-fórum](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Szalagtárak támogatása

- [A Microsoft Azure Key Vault alap függvénytár](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) biztosít **IKey** és **IKeyResolver** adaptert használjon a kulcsok azonosítóból keresése és kulccsal rendelkező műveletet hajt végre.
- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) kiterjesztett képességeket biztosít az Azure Key Vaulthoz.


