---
title: Az Azure Key Vault fejlesztői útmutatója
description: A fejlesztők a Azure Key Vault segítségével kezelhetik a titkosítási kulcsokat a Microsoft Azure környezetben.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 662c23a29e383800a4591c900e02133c16fa2090
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743318"
---
# <a name="azure-key-vault-developers-guide"></a>Az Azure Key Vault fejlesztői útmutatója

Key Vault lehetővé teszi a bizalmas adatok biztonságos elérését az alkalmazásokon belülről:

- A kulcsok, titkos kódok és tanúsítványok védelme a kód írása nélkül történik, és az alkalmazások egyszerűen használhatók.
- Lehetősége van arra, hogy az ügyfelek saját kulcsaikat, titkos kulcsokat és tanúsítványokat tudjanak kezelni, így az alapszintű szoftverek funkcióinak biztosítására koncentrálhat. Így az alkalmazásai nem felelnek meg az ügyfeleknek a bérlői kulcsokkal, titkokkal és tanúsítványokkal kapcsolatos felelősségének vagy lehetséges felelősségének.
- Az alkalmazás a kulcsokat az aláíráshoz és a titkosításhoz is használhatja, és az alkalmazásból külső kulcskezelő is megtartja. A kulcsokkal kapcsolatos további információkért lásd: [a kulcsok ismertetése](../keys/about-keys.md)
- Kezelheti a hitelesítő adatokat, például a jelszavakat, a hozzáférési kulcsokat, az olyan sas-jogkivonatokat, amelyeket az Key Vault a titokként tárol, lásd [a titkok](../secrets/about-secrets.md)
- Tanúsítványok kezelése. További információ: [a tanúsítványok ismertetése](../certificates/about-certificates.md)

További általános információk a Azure Key Vaultről: [Mi az Key Vault](overview.md).

## <a name="public-previews"></a>Nyilvános előzetes verziók

Időnként új Key Vault szolgáltatás nyilvános előzetes verzióját bocsátjuk rendelkezésére. Próbálja ki a nyilvános előzetes verzió funkcióit, és tudassa velünk, mit gondol azurekeyvault@microsoft.com , mi a visszajelzés e-mail-címe.

## <a name="creating-and-managing-key-vaults"></a>Kulcstartók létrehozása és kezelése

Az egyéb Azure-szolgáltatásokhoz hasonlóan Key Vault felügyelet Azure Resource Manager szolgáltatáson keresztül történik. Az Azure Resource Manager az Azure üzembehelyezési és felügyeleti szolgáltatása. Egy olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-fiókban lévő erőforrások létrehozását, frissítését és törlését. További információ: [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

A felügyeleti réteghez való hozzáférést az [Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview)szabályozza. Key Vault a felügyeleti réteg, más néven felügyeleti vagy vezérlési folyamat lehetővé teszi a kulcstartók és attribútumaik létrehozását és kezelését, beleértve a hozzáférési házirendeket, az adatsíkon felügyelt kulcsokat, titkokat és tanúsítványokat is. Az előre definiált `Key Vault Contributor` szerepkörökkel felügyeleti hozzáférést adhat a Key Vaulthoz.     

**A Key Vault felügyeletéhez szükséges API-k és SDK-k:**

| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault)<br>[Gyors útmutató](quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](quick-create-powershell.md)|[Referencia](/rest/api/keyvault/)|[Referencia](/azure/templates/microsoft.keyvault/vaults)|[Referencia](/dotnet/api/microsoft.azure.management.keyvault)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[Referencia](/java/api/com.microsoft.azure.management.keyvault)|[Referencia](/javascript/api/@azure/arm-keyvault)|

Lásd: [ügyféloldali kódtárak](client-libraries.md) telepítési csomagok és forráskódok számára.

További információ a Key Vault felügyeleti síkon: [Key Vault felügyeleti sík](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#management-plane-and-azure-rbac)

## <a name="authenticate-to-key-vault-in-code"></a>Hitelesítés Key Vault kódban

Key Vault olyan Azure AD-hitelesítést használ, amelyhez hozzáférést kell biztosítani az Azure AD rendszerbiztonsági tag számára. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy egyszerű alkalmazás, egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md), vagy bármilyen rendszerbiztonsági tag egy csoportja.

### <a name="authentication-best-practices"></a>Ajánlott eljárások a hitelesítéshez
Ajánlott felügyelt identitást használni az Azure-ban üzembe helyezett alkalmazásokhoz. Ha olyan Azure-szolgáltatásokat használ, amelyek nem támogatják a felügyelt identitást, vagy ha az alkalmazások üzembe helyezése a helyszínen történik, az [egyszerű szolgáltatásnév egy](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) lehetséges alternatíva. Ebben az esetben a tanúsítványt Key Vault kell tárolni, és gyakran el kell forgatni. A titkos kulccsal rendelkező szolgáltatásnév fejlesztési és tesztelési környezetekhez használható, valamint helyileg vagy Cloud Shell felhasználói rendszerbiztonsági tag használata ajánlott.

A fenti hitelesítési forgatókönyveket az Azure Identity Client Library támogatja, és integrálva van Key Vault SDK-val. Az Azure Identity Library különböző környezetekben és platformokon is használható a kód módosítása nélkül. Az Azure Identity azt is automatikusan lekéri a hitelesítési tokent, hogy bejelentkezett az Azure-felhasználónak az Azure CLI-vel, a Visual Studióval, a Visual Studio Code-val és másokkal. 

További információkért lásd: 

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)|     

## <a name="manage-keys-certificates-and-secrets"></a>Kulcsok, tanúsítványok és titkos kódok kezelése

A kulcsok, titkok és tanúsítványok elérését az adatsík vezérli. Az adatsík hozzáférés-vezérlése a helyi tároló hozzáférési házirendjei vagy a RBAC (előzetes verzió) használatával végezhető el.

**Kulcsok API-k és SDK-k**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/key)<br>[Gyors útmutató](../keys/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../keys/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#key-operations)|N/A|[Referencia](/dotnet/api/azure.security.keyvault.keys)|[Referencia](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[Gyors útmutató](../keys/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)|[Referencia](/javascript/api/@azure/keyvault-keys/)|

**Tanúsítványok API-k és SDK-k**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/certificate)<br>[Gyors útmutató](../certificates/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault)<br>[Gyors útmutató](../certificates/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#certificate-operations)|N/A|[Referencia](/dotnet/api/azure.security.keyvault.certificates)|[Referencia](/python/api/overview/azure/keyvault-certificates-readme)<br>[Gyors útmutató](../certificates/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)|[Referencia](/javascript/api/@azure/keyvault-certificates/)|

**Secrets API-k és SDK-k**


| Azure CLI | PowerShell | REST API | Resource Manager | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[Referencia](/cli/azure/keyvault/secret)<br>[Gyors útmutató](../secrets/quick-create-cli.md)|[Referencia](/powershell/module/az.keyvault/)<br>[Gyors útmutató](../secrets/quick-create-powershell.md)|[Referencia](/rest/api/keyvault/#secret-operations)|[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[Gyors útmutató](../secrets/quick-create-template.md)|[Referencia](/dotnet/api/azure.security.keyvault.secrets)<br>[Gyors útmutató](../secrets/quick-create-net.md)|[Referencia](/python/api/overview/azure/keyvault-secrets-readme)<br>[Gyors útmutató](../secrets/quick-create-python.md)|[Referencia](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[Gyors útmutató](../secrets/quick-create-java.md)|[Referencia](/javascript/api/@azure/keyvault-secrets/)<br>[Gyors útmutató](../secrets/quick-create-node.md)|

Lásd: [ügyféloldali kódtárak](client-libraries.md) telepítési csomagok és forráskódok számára.

További információ a Key Vault adatsík biztonságáról: [Key Vault adatsíkon és hozzáférési szabályzatok](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-access-policies) és [Key Vault adatsík és RBAC (előzetes verzió)](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault#data-plane-and-azure-rbac-preview)

### <a name="code-examples"></a>Kódpéldák

A Key Vault alkalmazással való használatának teljes példáit a következő témakörben tekintheti meg:

- [Azure Key Vault kód mintái](https://azure.microsoft.com/resources/samples/?service=key-vault) – a Azure Key Vaulthoz tartozó példák. 

## <a name="how-tos"></a>Használati útmutatók

A következő cikkek és forgatókönyvek feladat-specifikus útmutatást nyújtanak a Azure Key Vault használatához:

- A [tűzfal mögötti Key Vault elérése](access-behind-firewall.md) – egy kulcstartó eléréséhez a Key Vault-ügyfélalkalmazás számára a különböző funkciókhoz több végpontot is el kell érnie.
- Tanúsítványok telepítése virtuális gépekre Key Vault – [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows), [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) – az Azure-beli virtuális gépen futó felhőalapú alkalmazásnak tanúsítványra van szüksége. Hogyan szerezheti be ezt a tanúsítványt még ma a virtuális gépre?
- [Azure webalkalmazás-tanúsítvány üzembe helyezése Key Vault](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
- Hozzáférési szabályzat ([CLI](assign-access-policy-cli.md)  |  [PowerShell](assign-access-policy-powershell.md)  |  -[portál](assign-access-policy-portal.md)) társítása. 
- A [Key Vault Soft-delete használata a parancssori](soft-delete-cli.md) felülettel végigvezeti a kulcstartó használatának és életciklusának, valamint a helyreállítható törlést lehetővé tett különböző kulcstartó-objektumoknak a használatával.
- [Biztonságos értékek (például jelszavak)](../../azure-resource-manager/templates/key-vault-parameter.md) átadása az üzembe helyezés során – ha egy biztonságos értéket (például jelszót) kell megadni paraméterként az üzembe helyezés során, az értéket tárolhatja titkosként egy Azure Key Vault, és hivatkozhat az értékre más Resource Manager-sablonokban.

## <a name="integrated-with-key-vault"></a>Integrálva van Key Vault

Ezek a cikkek olyan egyéb forgatókönyvekkel és szolgáltatásokkal kapcsolatosak, amelyek a Key Vault-t használják vagy integrálják.

- [Inaktív titkosítás Key Vault](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) lehetővé teszi a saját bérlői kulcsának felettesét. Például ahelyett, hogy a Microsoft a bérlői kulcsát (az alapértelmezettet) kezelhesse, a saját bérlői kulcsát úgy kezelheti, hogy megfeleljen a szervezetére vonatkozó egyes előírásoknak. A saját bérlőkulcs felügyelete más néven a saját kulcs használata (BYOK).

## <a name="key-vault-overviews-and-concepts"></a>Key Vault áttekintések és fogalmak

- Key Vault a helyreállítható [törlési viselkedés](soft-delete-overview.md) olyan funkciót ismertet, amely lehetővé teszi a törölt objektumok helyreállítását, függetlenül attól, hogy a törlés véletlen vagy szándékos volt-e.
- [Key Vault az ügyfelek szabályozása](overview-throttling.md) a szabályozás alapvető fogalmait, és az alkalmazás megközelítését kínálja.
- [Key Vault biztonsági világok](overview-security-worlds.md) ismertetik a régiók és a biztonsági területek közötti kapcsolatokat.

## <a name="social"></a>Közösségi

- [Key Vault blog](https://aka.ms/kvblog)
- [Key Vault fórum](https://aka.ms/kvforum)
