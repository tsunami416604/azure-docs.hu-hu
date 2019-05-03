---
title: Az Azure Blockchain-szolgáltatás biztonsága
description: Az Azure Blockchain-szolgáltatás hozzáférés és biztonság – fogalmak
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: dd0a33364ed9395a85478798e47352c533bd47dc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028200"
---
# <a name="azure-blockchain-service-security"></a>Az Azure Blockchain-szolgáltatás biztonsága

Az Azure Blockchain-szolgáltatás számos Azure képességeit használja, hogy az adatok biztonságáról és elérhetőségéről. Adatok elkülönítése, titkosítási és hitelesítési használatával lett biztonságossá téve.

## <a name="isolation"></a>Elkülönítés

Azure Blockchain-szolgáltatás-erőforrások egy privát virtuális hálózaton legyenek különítve. Minden egyes tranzakciót és érvényesítés csomópontnak számít egy virtuális gépet (VM). Egy virtuális hálózaton belüli virtuális gépek nem kommunikálnak közvetlenül a virtuális gépeket egy másik virtuális hálózatot. Elkülönítés biztosítja a kommunikációs privát virtuális hálózaton belül marad. Az Azure virtuális hálózat elkülönítési további információkért lásd: [elkülönítés az Azure nyilvános Felhőjében](../../security/azure-isolation.md#networking-isolation).

![Virtuális hálózatok közötti diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Titkosítás

Az Azure storage-ban tárolt felhasználói adatok. Mozgásban lévő és a biztonság és titkosítás inaktív, a felhasználói adatokat is titkosítja. További információkért lásd: [Az Azure Storage biztonsági útmutatóját](../../storage/common/storage-security-guide.md).

## <a name="authentication"></a>Hitelesítés

Tranzakciók is kell küldeni a blockchain-csomópontok egy RPC-végponton keresztül. Ügyfelek, hogy kezeli a felhasználói hitelesítés egy tranzakció csomópont fordított proxykiszolgálón keresztül kommunikálnak, és SSL használatával titkosítja az adatokat.

![Hitelesítés diagramja](./media/data-security/authentication.png)

Nincsenek RPC hozzáférési hitelesítésének három mód.

### <a name="basic-authentication"></a>Alapszintű hitelesítés

Alapszintű hitelesítést használ egy HTTP-hitelesítést a felhasználónevet és jelszót tartalmazó fejléc. Felhasználói név a blockchain-csomópont nevét. A tag vagy a csomópont kiépítése során beállítva jelszó. A jelszó az Azure portal vagy a parancssori felület segítségével módosítható.

### <a name="access-keys"></a>Elérési kulcs

Hozzáférési kulcsok használata egy véletlenszerűen létrehozott karakterlánc szerepel a végpont URL-címe. Két kulcsot segítő kulcsrotálás. Kulcsok is az Azure portal és CLI legyenek újra létrehozva.

### <a name="azure-active-directory"></a>Azure Active Directory

Az Azure Active Directory (Azure AD) használja a jogcím-alapú hitelesítési módszer ahol a felhasználó hitelesítése az Azure AD az Azure AD-felhasználó hitelesítő adataival. Az Azure AD felhőalapú identitáskezelést nyújt, és lehetővé teszi a felhasználók egyetlen identitás használatára egy teljes vállalati és a hozzáférési alkalmazásokban, a felhőben. Azure Blockchain-szolgáltatás integrálható az Azure AD-azonosító összevonási, egyszeri bejelentkezést és a többtényezős hitelesítés engedélyezése. Hozzárendelhet felhasználókat, csoportokat és alkalmazás-szerepkörök blockchain tag és csomópont hozzáférést a szervezetében.

Az Azure AD-ügyfélproxy érhető el az [GitHub](https://github.com/Microsoft/azure-blockchain-connector/releases). Az ügyfél proxy irányítja a felhasználót az Azure AD bejelentkezési lapján, és beszerzi a sikeres hitelesítést követően tulajdonosi jogkivonattal. Ezt követően a felhasználó például Geth vagy Truffle Ethereum ügyfél alkalmazás csatlakozik az ügyfél-proxy végpont. Végül egy tranzakció elküldésekor az ügyfél proxy kódtárba a tulajdonosi jogkivonatot a HTTP-fejlécben, és a fordított proxy érvényesíti a jogkivonatot, OAuth protokollt használó.

## <a name="keys-and-ethereum-accounts"></a>Kulcsok és az Ethereum-fiókok

Az Azure Blockchain-szolgáltatás egyik tagja kiépítésekor egy Ethereum-fiókot és egy nyilvános és titkos kulcsból álló párként jön létre. A titkos kulcsot a blockchain tranzakciók küldendő szolgál. A Ethereum-fiók a nyilvános kulcs jogkivonatának az elmúlt 20 bájt. A Ethereum-fiók egy wallet is nevezik.

A privát és nyilvános kulcsból álló párként egy keyfile JSON formátumban van tárolva. A titkos kulcs titkosított használatával a blockchain Főkönyv szolgáltatás létrehozásakor megadott jelszót.

Titkos kulcsok tranzakciók digitális aláírására használhatók. Privát blokkláncok, a titkos kulccsal írja alá az intelligens szerződést az aláíró identitása jelöli. Az aláírás érvényességének ellenőrzéséhez a fogadó össze lehessen hasonlítani a nyilvános kulcsot az aláíró az aláírás alapján kiszámított címét.

Constellation kulcsokkal kvórum csomópont egyedi azonosításához. Constellation kulcsok akkor jönnek létre, a csomópont kiépítése időpontjában, és a egy privát tranzakciónak a kvórum privateFor paraméterében megadott.

## <a name="next-steps"></a>További lépések

[Az Azure Blockchain-szolgáltatás tranzakció csomópontok konfigurálása](configure-transaction-nodes.md)
