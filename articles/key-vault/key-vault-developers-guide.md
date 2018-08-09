---
title: Az Azure Key Vault fejlesztői útmutató
description: A fejlesztők az Azure Key Vault segítségével kezelheti a kriptográfiai kulcsokat, a Microsoft Azure-környezeten belül.
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 3a2ab6bbe2470fa12113ca4367316cf7a21190b8
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626728"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutató

A Key Vault lehetővé teszi a biztonságosan elérni az alkalmazásokból származó érzékeny információkhoz:

- Kulcsok és titkos kulcsok saját maga a kód írása nélkül védettek, és könnyen lehet a használatukkal az alkalmazások.
- Ön a saját ügyfele van, és a saját kulcsok kezelése, úgy, hogy az alapvető szoftverfunkciók koncentrálhat. Így az alkalmazások fog nem saját felelősséget és esetleges felelősségre az ügyfelek bérlői kulcsok és titkos kulcsok.
- Az alkalmazás kulcsok az aláíráshoz és a titkosítás még tartja a kulcskezelés külső az alkalmazásból, amely lehetővé teszi a megoldás egy földrajzilag elosztott alkalmazás megfelelő.
- A Key Vault 2016. szeptember megjelenésével kezdődően az alkalmazások mostantól használhatják a Key Vault [tanúsítványok](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). További információkért lásd: [kapcsolatos kulcsok, titkos kódok és tanúsítványok](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Az Azure Key Vault további általános információkért lásd: [Key vault](key-vault-whatis.md).

## <a name="public-previews"></a>Nyilvános előzetes verziók

Időről időre kiadunk egy nyilvános előzetes verziója egy új Key Vault szolgáltatás. Próbálja ki ezeket, és ossza meg velünk véleményét keresztül azurekeyvault@microsoft.com, a visszajelzési e-mail címet.

### <a name="storage-account-keys---july-10-2017"></a>A Tárfiókkulcsok – 2017. július 10.

>[!NOTE]
>A frissítés csak az Azure Key Vault a **Tárfiókkulcsok** funkció előzetes verzióban érhető el.

Ebben az előzetes verzióban az új Tárfiókkulcsok szolgáltatást tartalmaz, ezeket az adaptereket; keresztül elérhető [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) és [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Az új Tárfiókkulcsok szolgáltatás további információkért lásd: [tárfiókok kulcsainak áttekintése az Azure Key Vault](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videók

Ez a videó bemutatja, hogyan hozhat létre saját key vault és a "Hello Key Vault"-mintaalkalmazás használatával.

- [A Key Vault fejlesztői – gyors üzembe helyezési útmutató](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

A fenti videó erőforrások:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Az Azure Key Vault-mintakód](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Létrehozása és kezelése a Kulcstartók

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. A felügyeltszolgáltatás-identitás (MSI) segít leegyszerűsíteni ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia. 

Az MSI további információkért lásd: [Felügyeltszolgáltatás-identitás (MSI) Azure-erőforrások](https://docs.microsoft.com/azure/active-directory/msi-overview).

Aad-ben való használatáról további információkért lásd: [alkalmazások integrálása az Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

Mielőtt elkezdene dolgozni az kulcsok, titkos kódok és tanúsítványok tárol a kulcstárolóban, fog létrehozni, és kezelése a key vault – CLI, a PowerShell, a Resource Manager-sablonok vagy a REST, az alábbi cikkekben ismertetett módon:

- [Létrehozása és kezelése a Key vault-Kulcstartók CLI-vel](key-vault-manage-with-cli2.md)
- [Létrehozása és kezelése a Key vault-Kulcstartók a PowerShell-lel](key-vault-get-started.md)
- [Hozzon létre egy kulcstartót, és adjon hozzá egy titkos kulcsot az Azure Resource Manager-sablon használatával](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Létrehozása és kezelése a Key vault-Kulcstartók REST-tel](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kódolás a Key Vaulttal

A Key Vault-kezelési rendszer programozóknak több felület áll. Ez a szakasz az összes nyelven, valamint néhány kódot exampls mutató hivatkozásokat tartalmaz. 

### <a name="supported-programming-and-scripting-languages"></a>Támogatott programozási és parancsnyelven

#### <a name="rest"></a>REST

A Key Vault-erőforrások összes; REST-felületen keresztül érhető el tárolók, kulcsok, titkos kódok, stb. 

[Key Vault REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[.NET API-es hivatkozási a Key vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

A .NET SDK 2.x-es verziója további információkért lásd: a [kibocsátási megjegyzések](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[A Java SDK-t a Key Vault számára](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Node.js-ben a Key Vault-felügyeleti API-t és a Key Vault objektum API is külön. A következő áttekintő cikket is hozzáférést biztosít. 

[NODE.js-hez készült Azure Key Vault-modulok](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Pythonhoz készült Azure Key Vault-kódtárak](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Az Azure CLI a Key Vault számára](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Az Azure PowerShell, a Key Vault számára](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Rövid útmutatók

- [Kulcstartó létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Ismerkedés a Key Vault Node.js-ben](https://azure.microsoft.com/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Hitelesítésikód-példák

A Key Vault használata az alkalmazások teljes példákért lásd:

- [Az Azure Key Vault-Kódminták](http://www.microsoft.com/download/details.aspx?id=45343) – .NET mintaalkalmazás *HelloKeyVault* és a egy Azure-webalkalmazások példa. 
- [Használata egy webalkalmazásból az Azure Key Vault](key-vault-use-from-web-application.md) -oktatóanyag segítségével elsajátíthatja az Azure Key Vault használata egy webalkalmazásból az Azure-ban. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek adja meg a feladat jellemző útmutatást az Azure Key Vault használatához:

- [Kulcstartó Bérlőazonosítójának módosítása után az előfizetés áthelyezése](key-vault-subscription-move-fix.md) – Ha az Azure-előfizetés bérlőtől a B bérlőhöz áthelyezése a meglévő kulcstartók elérhetetlenné a B. Javítsa ki a jelen útmutató alapján bérlő rendszerbiztonsági tagjai (felhasználók és alkalmazások) válnak.
- [A Key Vault elérése tűzfal mögött található](key-vault-access-behind-firewall.md) – a key vault-ügyfélalkalmazásnak a különféle funkciók biztosításához több végpontok hozzáférhet kell key vault eléréséhez.
- [Létrehozása és az Azure Key Vault Transfer HSM-Protected kulcsok](key-vault-hsm-protected-keys.md) – Ez segít a tervezése, létrehozása, és utána a saját HSM által védett kulcsok az Azure Key Vault használata.
- [Hogyan lehet (például jelszavakat) biztonságos értékek továbbítása üzembe helyezés során](../azure-resource-manager/resource-manager-keyvault-parameter.md) – Ha teljesítenie kell a biztonságos értékének (például jelszót) paramétert a telepítés során az érték, egy Azure Key vaultban titkos kulcs tárolása, és hivatkozhat más erőforrás értéke Manager-sablonok.
- [Az SQL Server bővíthető kulcskezelés a Key Vault használata](https://msdn.microsoft.com/library/dn198405.aspx) – az SQL Server-összekötő az Azure Key Vault lehetővé teszi, hogy az SQL Server és SQL-az-a-VM kihasználhatja az Azure Key Vault szolgáltatás egy bővíthető Key Management (EKM) szolgáltató védelméhez annak titkosítási kulcsok alkalmazások rendszergazdájához. Transzparens adattitkosítás, a biztonsági mentés titkosítása és az oszlop a blokkszintű titkosítás.
- [Tanúsítványok központi telepítése virtuális gépekre a Key Vaultból](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – egy felhőalkalmazás-ben futó virtuális gép Azure kell egy tanúsítványt. Hogyan, be ezt a tanúsítványt a virtuális gépen még ma?
- [A Key Vault beállítása végpontok közötti kulcsforgatással és vizsgálattal együtt](key-vault-key-rotation-log-monitoring.md) – ez végigvezeti azon, hogyan állítható be a kulcsforgatás és az Azure Key Vault naplózását.
- [Üzembe helyezése az Azure webalkalmazás-tanúsítvány Key Vault segítségével]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) témakörből üzembe helyezésének részeként a Key Vaultban tárolt tanúsítványok [App Service-tanúsítvány](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) ajánlat.
- [Biztosítson engedélyt számú alkalmazás részére egy kulcstartó eléréséhez](key-vault-group-permissions-for-apps.md) Key Vault hozzáférés-vezérlési szabályzat csak a 16 bejegyzések támogatja. Ugyanakkor létrehozhat egy Azure Active Directory biztonsági csoportot is. A társított szolgáltatás rendszerbiztonsági tagok hozzáadása a biztonsági csoport, és ezután a Key Vault a biztonsági csoport hozzáférési jogot.
- További feladatspecifikus integrálása és és az Azure Key vault-Kulcstartók használatával, tekintse át [Ryan Jones Azure Resource Manager sablon példákat a Key vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [A Key Vault helyreállítható törlés használata a parancssori felület](key-vault-soft-delete-cli.md) végigvezeti Önt a használatát, és a key vault és a key vault-objektumokon életciklusát a helyreállítható törlés funkciójának engedélyezése.
- [A Key Vault helyreállítható törlés használata a PowerShell-lel](key-vault-soft-delete-powershell.md) végigvezeti Önt a használatát, és a key vault és a key vault-objektumokon életciklusát a helyreállítható törlés funkciójának engedélyezése.

## <a name="integrated-with-key-vault"></a>A Key Vault szolgáltatással integrált

Ezek a cikkek más forgatókönyvek és a szolgáltatásokat, vagy a Key Vault integrálása készül.

- [Az Azure Disk Encryption](../security/azure-security-disk-encryption.md) kihasználja az iparági szabvány [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) Windows szolgáltatása és a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) biztosít az operációs rendszer és az adatlemezek kötettitkosítását Linux funkcióját. A megoldás integrálva van az Azure Key Vault segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés, közben biztosítva, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure Storage-a.
- [Az Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) a fiókban tárolt adatok titkosítása megoldást kínál. Kulcskezelés, a Data Lake Store kétféle módon biztosítja a titkosítási főkulcsok (MEK), az adatokat a Data Lake Store-ban tárolt visszafejtés szükséges kezeléséhez. Vagy engedélyezheti a Data Lake Store a MEK kezeléséről, vagy is megtartja a MEK az Azure Key Vault-fiók használatával tulajdonjogát. Kulcskezelés módját egy Data Lake Store-fiók létrehozásakor adja meg. 
- [Az Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi, hogy manager a saját bérlőkulcsát. Ha például helyett a Microsoft felügyelné a bérlőkulcsát (alapértelmezett), kezelheti a saját bérlőkulcsát, hogy az megfeleljen a szervezetére vonatkozó speciális szabályozásoknak. A saját bérlőkulcs felügyelete más néven, a saját kulcs használata, azaz BYOK.

## <a name="key-vault-overviews-and-concepts"></a>A Key Vault áttekintéseket és fogalmak

- [A Key Vault helyreállítható törlés viselkedés](key-vault-ovw-soft-delete.md) ismerteti a szolgáltatás lehetővé teszi a törölt objektumok helyreállítása a törlés véletlen vagy szándékos volt-e.
- [Key Vault-ügyfélhez szabályozás](key-vault-ovw-throttling.md) csomagjainkkal szabályozás az alapvető fogalmait, és az alkalmazás egy megközelítést kínál.
- [Tárfiókok kulcsainak áttekintése a Key Vault](key-vault-ovw-storage-keys.md) a Key Vault integration Azure Storage-fiókok kulcsok ismerteti.
- [Key Vault biztonsági világai](key-vault-ovw-security-worlds.md) a régiók és biztonsági területek közötti kapcsolatokat ismerteti.

## <a name="social"></a>Közösségi tartalom

- [A Key Vault-Blog](http://aka.ms/kvblog)
- [A Key Vault-fórum](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Szalagtárak támogatása

- [A Microsoft Azure Key Vault alap függvénytár](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) biztosít **Rendszerállapotkulcsot** és **IKeyResolver** felületek azonosítók helyenk megkeresése és műveleteket végez a kulcsokat.
- [Microsoft Azure Key Vault-bővítmények](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) kiterjesztett képességeket biztosít az Azure Key Vault.


