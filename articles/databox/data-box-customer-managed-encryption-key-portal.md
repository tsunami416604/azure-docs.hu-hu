---
title: Az ügyfél által felügyelt kulcsok konfigurálása a Azure Portal használatával Azure Data Box
description: Megtudhatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat a Azure Portal az Azure Data Box-Azure Key Vault használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 05/07/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 31147d534109e0d74d33d102075c69eeb703496e
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739935"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Ügyfél által felügyelt kulcsok használata Azure Key Vaultban Azure Data Box

Azure Data Box védi az eszköz zárolási kulcsát (más néven eszköz jelszavát), amely az eszköz titkosítási kulccsal történő zárolására szolgál. Alapértelmezés szerint egy Data Box-rendelés eszközének feloldási kulcsa egy Microsoft által felügyelt kulccsal van titkosítva. Az eszköz feloldási kulcsának további szabályozásához megadhatja az ügyfél által felügyelt kulcsot is. 

Az ügyfél által felügyelt kulcsokat egy Azure Key Vault kell létrehozni és tárolni. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan használhatók az ügyfél által felügyelt kulcsok a [Azure Portal](https://portal.azure.com/)Azure Data Boxával. Ez a cikk Azure Data Box eszközökre és Azure Data Box Heavy eszközökre egyaránt vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Azure Data Box sorrendet hozott létre az [oktatóanyag: order Azure Data Box](data-box-deploy-ordered.md)utasításban szereplő utasítások szerint.

2. Van egy meglévő Azure Key Vault egy kulccsal, amely az eszköz feloldási kulcsának védelemére használható. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót a Azure Portal használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a Azure Portal használatával](../key-vault/secrets/quick-create-portal.md).

    - A **Soft delete** és a **not Purge** beállítása a meglévő kulcstartóra van beállítva. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve. A tulajdonságok engedélyezéséhez tekintse meg a következő cikkek egyikében a **Soft-delete engedélyezése** és a **kiürítési védelem engedélyezése** című szakaszt:

        - [A Soft-delete használata a PowerShell-](../key-vault/general/soft-delete-powershell.md)lel.
        - [A Soft delete használata a parancssori](../key-vault/general/soft-delete-cli.md)felülettel.
    - A meglévő Key Vault 2048 méretű RSA-kulccsal kell rendelkeznie. A kulcsokkal kapcsolatos további információkért tekintse meg [a Azure Key Vault kulcsok](../key-vault/keys/about-keys.md)című témakört.
    - A Key vaultnak ugyanabban a régióban kell lennie, mint az adataihoz használt Storage-fiókoknak. Több Storage-fiók is csatolható a Azure Data Box-erőforráshoz.
    - Ha nem rendelkezik meglévő kulcstartóval, azt is létrehozhatja a következő szakaszban leírtak szerint.

## <a name="enable-keys"></a>Kulcsok engedélyezése

Az ügyfél által felügyelt kulcs konfigurálása a Azure Data Box esetében nem kötelező. Alapértelmezés szerint a Data Box Microsoft által felügyelt kulccsal védi a BitLocker-kulcsot. A Azure Portal ügyfél által felügyelt kulcsának engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Data Box sorrendjét az **Áttekintés** panelen.

    ![Data Box sorrend áttekintő panelje](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Lépjen a **beállítások > titkosítás menüpontra**. A **titkosítás típusa**területen kiválaszthatja, hogyan szeretné védelemmel ellátni az eszköz zárolási kulcsát. Alapértelmezés szerint a rendszer egy Microsoft által felügyelt kulcsot használ az eszköz feloldási jelszavának biztosításához. 

    ![Titkosítási lehetőség kiválasztása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-2.png)

3. Válassza a titkosítási típust **ügyfél által felügyelt kulcsként**. Miután kiválasztotta az ügyfél által felügyelt kulcsot, **válasszon ki egy kulcstartót és egy kulcsot**.

    ![Ügyfél által felügyelt kulcs kiválasztása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

5. Az előfizetést a Azure Key Vault panelen a **kulcs kiválasztásával** automatikusan kitölti a rendszer. A **Key Vault**esetében választhat egy meglévő kulcstartót a legördülő listából.

    ![Új Azure Key Vault létrehozása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-31.png)

    Új kulcstartó létrehozásához az **új létrehozása** lehetőséget is választhatja. A **Key Vault létrehozása**panelen adja meg az erőforráscsoportot és a kulcstároló nevét. Győződjön meg arról, hogy a helyreállítható **törlési** és törlési **védelem** engedélyezve van. Fogadja el az összes többi alapértelmezett beállítást. Válassza a **felülvizsgálat + létrehozás**lehetőséget.

    ![Új Azure Key Vault létrehozása 2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

7. Tekintse át a kulcstartóhoz tartozó információkat, és válassza a **Létrehozás**lehetőséget. Várjon pár percet, amíg a Key Vault létrehozása befejeződik.

    ![Azure Key Vault létrehozása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

8. A **válasszon kulcsot Azure Key Vault közül**választhat egy kulcsot a meglévő kulcstartóban.

    ![Új kulcs létrehozása Azure Key Vault 3](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

9. Ha új kulcsot szeretne létrehozni, válassza az **új létrehozása** elemet a kulcs létrehozásához. Az RSA-kulcs mérete 2048 vagy nagyobb lehet.

    ![Új kulcs létrehozása Azure Key Vault 4](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-61.png)

10. Adja meg a kulcs nevét, fogadja el a többi alapértelmezett értéket, majd válassza a **Létrehozás**lehetőséget. 

    ![Új kulcs létrehozása](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)


11. Értesítést kap arról, hogy a Key vaultban létrejön egy kulcs. Válassza ki a **verziót** , majd válassza a **kiválasztás**lehetőséget.

    ![A Key vaultban létrehozott új kulcs](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

12. A **titkosítás típusa** ablaktáblán látható a Key Vault és az ügyfél által felügyelt kulcshoz kiválasztott kulcs.

    ![Kulcs és kulcstartó az ügyfél által felügyelt kulcshoz](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

13. Mentse a kulcsot. 

    ![Ügyfél által felügyelt kulcs mentése](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    A kulcs URL-címe a **titkosítás típusa**alatt jelenik meg.

    ![Ügyfél által felügyelt kulcs URL-címe](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)

> [!IMPORTANT]
> Letilthatja a Microsoft által felügyelt kulcsot, és áthelyezheti az ügyfél által felügyelt kulcsra a Data Box sorrend bármely szakaszában. Az ügyfél által felügyelt kulcs létrehozása után azonban nem válthat vissza a Microsoft által felügyelt kulcsra.

## <a name="troubleshoot-errors"></a>Hibák elhárítása

Ha az ügyfél által felügyelt kulccsal kapcsolatos hibákat kap, a hibaelhárításhoz használja a következő táblázatot.

| Hibakód| A hiba részletei| Helyreállítható?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Nem sikerült beolvasni a hitelesítő kulcsot, mert a felhasználó által felügyelt kulcs le van tiltva.| Igen, a kulcs verziójának engedélyezésével.|
| SsemUserErrorEncryptionKeyExpired| A hitelesítő kulcs beolvasása sikertelen volt, mert az ügyfél által felügyelt kulcs lejárt.| Igen, a kulcs verziójának engedélyezésével.|
| SsemUserErrorKeyDetailsNotFound| Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcs.| Ha törölte a kulcstartót, az ügyfél által felügyelt kulcs nem állítható helyre.  Ha áttelepítette a kulcstartót egy másik bérlőre, tekintse meg a [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../key-vault/general/move-subscription.md)című témakört. Ha törölte a Key vaultot:<ol><li>Igen, ha a kiürítés-védelem időtartama alatt van, a [Key Vault helyreállításának](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)lépéseit követve.</li><li>Nem, ha meghaladja a kiürítési-védelmi időtartamot.</li></ol><br>Ha a Key Vault bérlői áttelepítést hajtott végre, igen, az alábbi lépések egyikével állítható helyre: <ol><li>A Key Vault visszaállítása a régi bérlőre.</li><li>Állítsa be `Identity = None` , majd állítsa vissza az értéket `Identity = SystemAssigned` . Ezzel törli és újból létrehozza az identitást az új identitás létrehozása után. A `Get` `Wrap` `Unwrap` Key Vault hozzáférési házirendjében engedélyezze az új identitás engedélyezését és engedélyeit.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException| A hitelesítő kulcs beolvasása sikertelen, mert a rendszer visszavonja az ügyfél által felügyelt kulcshoz való hozzáférést.| Igen, ellenőrizze, hogy: <ol><li>A Key Vault továbbra is az MSI szerepel a hozzáférési házirendben.</li><li>A hozzáférési házirend engedélyeket biztosít a beolvasáshoz, a becsomagoláshoz, a kicsomagoláshoz.</li><li>Ha a Key Vault a tűzfal mögötti vNet van, ellenőrizze, hogy engedélyezve van-e a **Microsoft megbízható szolgáltatásainak engedélyezése** lehetőség.</li></ol>|
| SsemUserErrorKeyVaultDetailsNotFound| Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcshoz tartozó kulcstároló. | Ha törölte a kulcstartót, az ügyfél által felügyelt kulcs nem állítható helyre.  Ha áttelepítette a kulcstartót egy másik bérlőre, tekintse meg a [Key Vault-bérlő azonosítójának módosítása az előfizetés áthelyezése után](../key-vault/general/move-subscription.md)című témakört. Ha törölte a Key vaultot:<ol><li>Igen, ha a kiürítés-védelem időtartama alatt van, a [Key Vault helyreállításának](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault)lépéseit követve.</li><li>Nem, ha meghaladja a kiürítési-védelmi időtartamot.</li></ol><br>Ha a Key Vault bérlői áttelepítést hajtott végre, igen, az alábbi lépések egyikével állítható helyre: <ol><li>A Key Vault visszaállítása a régi bérlőre.</li><li>Állítsa be `Identity = None` , majd állítsa vissza az értéket `Identity = SystemAssigned` . Ezzel törli és újból létrehozza az identitást az új identitás létrehozása után. A `Get` `Wrap` `Unwrap` Key Vault hozzáférési házirendjében engedélyezze az új identitás engedélyezését és engedélyeit.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Nem sikerült beolvasni a hitelesítő kulcsot, mert nem található az ügyfél által felügyelt kulcs.| Igen, ellenőrizze, hogy: <ol><li>A Key Vault továbbra is az MSI szerepel a hozzáférési házirendben.</li><li>Az identitás típusa rendszer van hozzárendelve.</li><li>Engedélyezze a Get, a wrap és a dewrap engedélyeket a Key Vault hozzáférési házirendjében lévő identitáshoz.</li></ol>|
| Általános hiba  | A hitelesítő kulcs beolvasása sikertelen.| Ez egy általános hiba. A hiba megoldásához forduljon a Microsoft ügyfélszolgálatahoz, és határozza meg a következő lépéseket.|


## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault](../key-vault/general/overview.md)?
