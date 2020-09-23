---
title: A Soft delete minden Azure Key Vaulton engedélyezve lesz | Microsoft Docs
description: Ez a dokumentum fogadja el az összes kulcstartó törlését.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: sudbalas
ms.openlocfilehash: bf758a07cff248fc0da3f279e68a14e88797e382
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90984596"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>A Soft-delete minden kulcstartón engedélyezve lesz.

> [!WARNING]
> **Megszakítási változás**: a rendszer az év végéig elavulttá teszi a helyreállítható törlés lehetőségét, és az összes kulcstartó esetében automatikusan bekapcsolja a törlési védelmet.  Azure Key Vault felhasználóknak és rendszergazdáknak azonnal engedélyeznie kell a helyreállítható törlést a kulcstartón.
>
> A felügyelt HSM esetében a Soft delete alapértelmezés szerint engedélyezve van, és nem tiltható le.

Ha törölnek egy titkos kulcsot a kulcstartóból, és nem törli a védelmet, a titkos kulcs véglegesen törlődik. A felhasználók jelenleg a Key Vault létrehozásakor letiltják a helyreállítható törlést, de a titkos kódok védelme érdekében a felhasználók véletlenül vagy rosszindulatúan törölhetik a felhasználókat, a Microsoft hamarosan lehetővé teszi az **összes** kulcstartóban lévő helyreállítható védelem törlését, és a felhasználók már nem választhatják ki vagy kapcsolhatják le a törlési funkciót.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<helyettesítő szöveg>":::

A Soft-delete funkció részletes ismertetését lásd: [Azure Key Vault-törlés – áttekintés](soft-delete-overview.md).

## <a name="how-do-i-respond-to-breaking-changes"></a>Hogyan válasz a változtatások megszakítására

Egy Key Vault-objektum nem hozható létre ugyanazzal a névvel, mint egy Key Vault-objektum a nem törölt állapotban.  Ha például töröl egy nevű kulcsot `test key` a Key Vault a-ban, akkor a Key Vault a-ben nevű új kulcsot nem hozhatja létre, amíg a helyreállított objektumot el nem `test key` `test key` távolítja.

### <a name="application-changes"></a>Alkalmazás módosításai

Ha az alkalmazás azt feltételezi, hogy a helyreállítható törlés nincs engedélyezve, és azt várja, hogy a törölt titkos kód vagy a kulcstároló neve azonnal újra elérhető legyen, az alkalmazás logikájának az alábbi módosításokat kell elvégeznie ahhoz, hogy elfogadja ezt a változást.

1. Az eredeti kulcstartó vagy titkos kód törlése
2. Törölje a Key vaultot vagy a titkos kulcsot a helyreállított állapotból.
3. Várakozás – az azonnali újbóli létrehozás ütközést okozhat.
4. Hozza létre újra a kulcstárolót ugyanazzal a névvel.
5. Implementálja újra a műveletet, ha a létrehozási művelet továbbra is névütközés-hibát eredményez, akár 10 percet is igénybe vehet, amíg a DNS-rekordok frissülnek a legrosszabb esetben.

### <a name="administration-changes"></a>Adminisztrációs változások

A titkos kulcsok végleges törléséhez hozzáférést igénylő rendszerbiztonsági tageknek további hozzáférési házirendre van szükségük a titkos kulcsok és a kulcstartó törléséhez.

Ha van olyan Azure Policy a kulcstartóban, amely a helyreállítható törlés kikapcsolását bízza meg, akkor ezt a szabályzatot le kell tiltani.  Előfordulhat, hogy ezt a problémát egy olyan rendszergazdának kell megadnia, aki a környezetre alkalmazott Azure-szabályzatokat vezérli. Ha ez a házirend nincs letiltva, elveszítheti az új kulcstartók létrehozását az alkalmazott házirend hatókörében.

Ha a szervezete jogi megfelelőségi követelmények hatálya alá tartozik, és a törölt kulcstartók és titkos kódok nem maradhatnak helyreállítható állapotban, hosszú időn keresztül módosítania kell a helyreállítható törlés megőrzési időtartamát, amely 7 – 90 nap között állítható be, hogy megfeleljen a szervezet szabványainak.

## <a name="procedures"></a>Eljárások

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>A kulcstárolók naplózásával ellenőrizze, hogy engedélyezve van-e a Soft delete

1. Jelentkezzen be az Azure Portalra.
2. Keressen rá a "Azure Policy" kifejezésre.
3. Válassza a "definíciók" lehetőséget.
4. A kategória területen válassza a "Key Vault" elemet a szűrőben.
5. Válassza ki a "Key Vault objektumok helyreállítható" házirendet.
6. Kattintson a "hozzárendelés" gombra.
7. Állítsa be a hatókört az előfizetésre.
8. Válassza a "felülvizsgálat + létrehozás" lehetőséget.
9. A-ben akár 24 óráig is elvégezheti a környezet teljes vizsgálatát.
10. A Azure Policy panelen kattintson a "megfelelőség" elemre.
11. Válassza ki az alkalmazott szabályzatot.

Most már képesnek kell lennie szűrni, és látni, hogy a kulcstárolók melyike legyen engedélyezve a helyreállítható törlés (megfelelő erőforrások), és hogy mely kulcstartók esetében nem engedélyezett a helyreállítható törlés (nem megfelelő erőforrások).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Meglévő kulcstartóhoz tartozó Soft delete bekapcsolása

1. Jelentkezzen be az Azure Portalra.
2. Keresse meg a Key Vault.
3. Válassza a tulajdonságok lehetőséget a beállítások területen.
4. A helyreállítható törlés területen jelölje be a "tároló és az objektumok helyreállításának engedélyezése" választógombot.
5. Állítsa be a megőrzési időszakot a Soft delete értékre.
6. Válassza a Save (Mentés) lehetőséget.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Engedélyek kiürítésének engedélyezése a rendszerbiztonsági tag számára

1. Jelentkezzen be az Azure Portalra.
2. Keresse meg a Key Vault.
3. A beállítások területen válassza a "hozzáférési szabályzatok" lehetőséget.
4. Válassza ki azt a szolgáltatásnevet, amelyhez hozzáférést szeretne biztosítani.
5. A Key, a Secret és a Certificate engedély alatti legördülő menüben görgessen le az "emelt szintű műveletek" elemre, és válassza a "kiürítés" engedélyt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-change-affect-me"></a>Befolyásolja a változás a változást?

Ha már van bekapcsolva a helyreállítható törlés, vagy ha nem törli és nem hozza létre újra a Key Vault-objektumokat ugyanazzal a névvel, akkor a Key Vault viselkedésében valószínűleg nem fog megjelenni változás.

Ha olyan alkalmazással rendelkezik, amely gyakran azonos elnevezési konvenciókkal törli és újból létrehozza a Key Vault-objektumokat, akkor a várt működés érdekében módosítania kell az alkalmazás logikáját. Tekintse meg a "Hogyan válasz a változtatások feltörésére?" című témakört. fenti szakasz.

### <a name="how-do-i-benefit-from-this-change"></a>Hogyan kihasználni ezt a változást?

A Soft delete Protection a szervezet számára további védelmi réteget biztosít a véletlen vagy rosszindulatú törlés ellen. Key Vault-rendszergazdaként korlátozhatja a hozzáférést az engedélyek és a kiürítési engedélyek esetében is.

Ha egy felhasználó véletlenül töröl egy kulcstartót vagy titkos kulcsot, megadhatja nekik hozzáférési engedélyeket a titkos kód helyreállításához anélkül, hogy a titkos kulcsot vagy a kulcstárolót véglegesen törli. Ez a saját kiszolgálási folyamat a környezetében a lehető legkevesebb időt fogja csökkenteni, és garantálja a titkok rendelkezésre állását.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hogyan megtudni, ha lépéseket kell tenniük?

Kövesse az "eljárás a kulcstartók naplózására" című szakaszban ismertetett lépéseket, és ellenőrizze, hogy a Soft delete be van-e kapcsolva. A módosítás hatással van minden olyan kulcstartóra, amely nem rendelkezik a helyreállítható törléssel. A naplózást segítő további eszközök hamarosan elérhetők lesznek, és ez a dokumentum frissül.

### <a name="what-action-do-i-need-to-take"></a>Milyen műveleteket kell elvégeznie?

Győződjön meg arról, hogy nem kell módosítania az alkalmazás logikáját. Ha meggyőződött róla, kapcsolja be a Soft-delete szolgáltatást az összes kulcstartón. Ezzel a lépéssel meggyőződhet arról, hogy az év végén az összes kulcstartó esetében nem lesz hatással a feltörési változás, ha a törlés be van kapcsolva.

### <a name="by-when-do-i-need-to-take-action"></a>Mire van szükség a művelet elvégzéséhez?

A rendszer az év végéig bekapcsolja az összes kulcstartót a Soft delete használatával. Annak érdekében, hogy az alkalmazások ne legyenek hatással, kapcsolja be a helyreállítható törlést a kulcstartón a lehető leghamarabb.

## <a name="what-will-happen-if-i-dont-take-any-action"></a>Mi történik, ha nem végezek semmilyen műveletet?

Ha nem hajt végre semmilyen műveletet, a rendszer az év végén automatikusan bekapcsolja az összes kulcstartót. Ez ütközési hibákhoz vezethet, ha egy Key Vault-objektumot próbál törölni, és újból létrehozza azt ugyanazzal a névvel anélkül, hogy előbb törölje azt a helyreállított állapotból. Ennek hatására előfordulhat, hogy az alkalmazások vagy az Automation meghibásodik.

## <a name="next-steps"></a>Következő lépések

- Lépjen kapcsolatba velünk a változással kapcsolatos bármilyen kérdéssel [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) .
- A [Soft-delete áttekintésének](soft-delete-overview.md) beolvasása
