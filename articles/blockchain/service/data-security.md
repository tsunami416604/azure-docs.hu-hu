---
title: Az Azure Blockchain szolgáltatás biztonsága
description: Az Azure Blockchain Szolgáltatás adathozzáférése és biztonsági fogalmai
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879598"
---
# <a name="azure-blockchain-service-security"></a>Az Azure Blockchain szolgáltatás biztonsága

Az Azure Blockchain Service számos Azure-funkciót használ az adatok biztonságának és elérhetővé tétele érdekében. Az adatok elkülönítés, titkosítás és hitelesítés használatával védettek.

## <a name="isolation"></a>Elkülönítés

Az Azure Blockchain Service erőforrásai egy privát virtuális hálózatban vannak elkülönítve. Minden tranzakció és érvényesítési csomópont egy virtuális gép (VM). Egy virtuális hálózat ban lévő virtuális gépek nem tudnak közvetlenül kommunikálni egy másik virtuális hálózat virtuális gépeivel. Az elkülönítés biztosítja, hogy a kommunikáció magánjellegű maradjon a virtuális hálózaton belül. Az Azure virtuális hálózatok elkülönítésével kapcsolatos további információkért tekintse meg [az elkülönítést az Azure nyilvános felhőben című témakörben.](../../security/fundamentals/isolation-choices.md#networking-isolation)

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Titkosítás

A felhasználói adatok az Azure storage-ban tárolódnak. A felhasználói adatok titkosítva vannak mozgásban és nyugalomban a biztonság és a titoktartás érdekében. További információ: [Azure Storage biztonsági útmutató.](../../storage/blobs/security-recommendations.md)

## <a name="authentication"></a>Hitelesítés

A tranzakciók elküldhetők a blokklánc-csomópontoknak egy RPC-végponton keresztül. Az ügyfelek egy tranzakciós csomóponttal kommunikálnak egy fordított proxykiszolgáló használatával, amely kezeli a felhasználói hitelesítést és titkosítja az adatokat a TLS-en keresztül.

![Hitelesítési diagram](./media/data-security/authentication.png)

Az RPC-hozzáférésnek három hitelesítési módja van.

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Az alapfokú hitelesítés a felhasználónevet és a jelszót tartalmazó HTTP-hitelesítési fejlécet használja. A felhasználónév a blokklánc-csomópont neve. A jelszó egy tag vagy csomópont kiépítése során van beállítva. A jelszó az Azure Portalon vagy a CLI-n keresztül módosítható.

### <a name="access-keys"></a>Elérési kulcs

Az Access-kulcsok a végpont URL-címében található véletlenszerűen generált karakterláncot használják. Két hozzáférési kulcs segít a kulcselforgatás engedélyezésében. A kulcsok újragenerálhatók az Azure Portalról és a CLI-ről.

### <a name="azure-active-directory"></a>Azure Active Directory

Az Azure Active Directory (Azure AD) egy jogcímalapú hitelesítési mechanizmust használ, ahol a felhasználót az Azure AD az Azure AD felhasználói hitelesítő adataival hitelesíti. Az Azure AD felhőalapú identitáskezelést biztosít, és lehetővé teszi az ügyfelek számára, hogy egyetlen identitást használjanak a teljes vállalaton belül, és hozzáférjenek a felhőben lévő alkalmazásokhoz. Az Azure Blockchain Szolgáltatás integrálható az Azure AD-vel, amely lehetővé teszi az azonosító-összevonást, az egyszeri bejelentkezést és a többtényezős hitelesítést. A szervezetben felhasználókat, csoportokat és alkalmazásszerepköröket rendelhet a blokklánc-taghoz és a csomóponthoz.

Az Azure AD-ügyfélproxy elérhető a [GitHubon.](https://github.com/Microsoft/azure-blockchain-connector/releases) Az ügyfélproxy irányítja a felhasználót az Azure AD bejelentkezési lapra, és sikeres hitelesítés esetén beszerzi a tulajdonosi jogkivonatot. Ezt követően a felhasználó csatlakozik egy Ethereum ügyfélalkalmazás, például a Geth vagy a Szarvasgomba az ügyfél proxy végpontját. Végül egy tranzakció elküldésekor az ügyfélproxy befecskendezi a tulajdonosi jogkivonatot a http fejlécben, és a fordított proxy érvényesíti a jogkivonatot az OAuth protokoll használatával.

## <a name="keys-and-ethereum-accounts"></a>Kulcsok és Ethereum-fiókok

Az Azure Blockchain szolgáltatás tagjának kiépítésekor létrejön egy Ethereum-fiók, valamint egy nyilvános és személyes kulcspár. A személyes kulcs tranzakciók küldésére szolgál a blokkláncba. Az Ethereum-fiók a nyilvános kulcs kivonatának utolsó 20 bájtja. Az Ethereum fiókot pénztárcának is nevezik.

A személyes és a nyilvános kulcspár JSON formátumban kulcsfájlként van tárolva. A személyes kulcs titkosítva van a blockchain főkönyvi szolgáltatás létrehozásakor megadott jelszóval.

A személyes kulcsok tranzakciók digitális aláírására szolgálnak. A privát blokkláncokban a személyes kulcs által aláírt intelligens szerződés jelenti az aláíró identitását. Az aláírás érvényességének ellenőrzéséhez a fogadó összehasonlíthatja az aláíró nyilvános kulcsát az aláírásból kiszámított címmel.

A konstellációs billentyűk a Kvórum-csomópont egyedi azonosítására szolgálnak. A konstellációs kulcsok a csomópont-kiépítés időpontjában jönnek létre, és a kvórumban lévő privát tranzakció paraméterében vannak megadva.

## <a name="next-steps"></a>További lépések

Lásd: [Az Azure Active Directory-hozzáférés konfigurálása az Azure Blockchain szolgáltatáshoz.](configure-aad.md)
