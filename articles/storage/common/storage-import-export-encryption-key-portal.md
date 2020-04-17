---
title: Az Azure Portal használata az ügyfél által felügyelt kulcsok konfigurálásához az importálási/exportálási szolgáltatáshoz
description: Megtudhatja, hogy az Azure Portal használatával hogyan konfigurálhatja az ügyfelek által felügyelt kulcsokat az Azure Key Vault for Azure import/export szolgáltatással. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlők létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: ddcb47bfe8ba2b77efd8ff0aed52f1412107f0c5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456498"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Ügyfél által felügyelt kulcsok használata az Azure Key Vaultban importálási/exportálási szolgáltatáshoz

Az Azure Import/Export védi a meghajtók titkosítási kulcson keresztül a meghajtók zárolásához használt BitLocker-kulcsokat. Alapértelmezés szerint a BitLocker-kulcsok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához ügyfél által kezelt kulcsokat is biztosíthat.

Az ügyfél által felügyelt kulcsokat létre kell hozni, és egy Azure Key Vaultban kell tárolni. Az Azure Key Vaultról a [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

Ez a cikk bemutatja, hogyan használhatja az ügyfél által felügyelt kulcsokat az Importálás/exportálás szolgáltatással az [Azure Portalon.](https://portal.azure.com/)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. A következő utasításoknak megfelelően hozott létre importálási vagy exportálási feladatot:

    - [Hozzon létre egy importálási feladatot a blobok számára.](storage-import-export-data-to-blobs.md)
    - [Hozzon létre egy importálási feladatot a fájlokhoz.](storage-import-export-data-to-files.md)
    - [Exportálási feladat létrehozása blobok számára](storage-import-export-data-from-blobs.md)

2. Van egy meglévő Azure Key Vault egy kulcs, amely a BitLocker-kulcs védelmére használható. Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure Portalon, olvassa el a [rövid útmutató: Az Azure Key Vault titkos kulcsának beállítása és beolvasása az Azure Portalon.](../../key-vault/secrets/quick-create-portal.md)

    - **A helyreállítható törlés** és **a Ne ürítés** beállítás a meglévő Key Vaulton van beállítva. Ezek a tulajdonságok alapértelmezés szerint nincsenek engedélyezve. Ezeknek a tulajdonságoknak az **engedélyezéséről** és a **kiürítési védelem engedélyezése** című szakaszokból az alábbi cikkek egyikében található:

        - [A soft-delete használata a PowerShell használatával.](../../key-vault/general/soft-delete-powershell.md)
        - [A soft-delete használata a CLI-vel.](../../key-vault/general/soft-delete-cli.md)
    - A meglévő key vault rendelkeznie kell egy RSA-kulcs 2048 méretű vagy annál nagyobb. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **kulcstárolók** című témakörben talál.
    - A key vault nak ugyanabban a régióban kell lennie, mint az adatok tárfiókjának.  
    - Ha nem rendelkezik egy meglévő Azure Key Vault, is létrehozhatja a következő szakaszban leírtak szerint.

## <a name="enable-keys"></a>Kulcsok engedélyezése

Az ügyfél által felügyelt kulcs konfigurálása az importálási/exportálási szolgáltatáshoz nem kötelező. Alapértelmezés szerint az Importálás/exportálás szolgáltatás microsoft által kezelt kulcsot használ a BitLocker kulcs védelmére. Az ügyfél által felügyelt kulcsok engedélyezéséhez az Azure Portalon kövesse az alábbi lépéseket:

1. Nyissa meg az **Importálási** feladat Áttekintés paneljét.
2. A jobb oldali ablaktáblában válassza **a BitLocker-kulcsok titkosításának kiválasztása**lehetőséget.

    ![Titkosítási beállítás kiválasztása](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. A **Titkosítás** panelen megtekintheti és másolhatja az eszköz BitLocker kulcsát. A **Titkosítás típusa csoportban**megadhatja, hogyan szeretné védeni a BitLocker kulcsot. Alapértelmezés szerint a Microsoft által felügyelt kulcs használatos.

    ![BitLocker-kulcs megtekintése](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Megadhatja a vevő által kezelt kulcsot. Miután kiválasztotta az ügyfél által kezelt kulcsot, válassza ki a **kulcstartót és egy kulcsot.**

    ![Vevő által kezelt kulcs kiválasztása](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. Az **Azure Key Vault panelkiválasztása kulcs** panelen az előfizetés automatikusan kitöltődik. A **Key Vault**, kiválaszthat egy meglévő key vault a legördülő listából.

    ![Az Azure Key Vault kijelölése vagy létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Új kulcstartó létrehozásához válassza az **Új létrehozása lehetőséget** is. A **Key Vault létrehozása panelen**adja meg az erőforráscsoportot és a key vault nevét. Fogadja el az összes többi alapértelmezést. Válassza **a Véleményezés + Létrehozás lehetőséget.**

    ![Új Azure Key Vault létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Tekintse át a kulcstartóhoz társított információkat, és válassza a **Létrehozás gombot.** Várjon néhány percet, amíg a kulcstartó létrehozása befejeződik.

    ![Azure Key Vault létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. Az **Azure Key Vault ból select kulcs**kiválasztásához kiválaszthat egy kulcsot a meglévő kulcstartóban.

9. Ha új kulcstartót hozott létre, válassza az **Új létrehozása lehetőséget** a kulcs létrehozásához. Az RSA-kulcs mérete 2048 vagy nagyobb lehet.

    ![Új kulcs létrehozása az Azure Key Vaultban](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Ha a kulcstartó létrehozásakor a helyreállítható törlési és törlési védelem nincs engedélyezve, a kulcstartó frissül, hogy a rendszer engedélyezve legyen a helyreállítható törlési és törlési védelem.

10. Adja meg a kulcs nevét, fogadja el a többi alapértelmezést, és válassza a **Létrehozás gombot.**

    ![Új kulcs létrehozása](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Válassza a **Verzió lehetőséget,** majd válassza a **Kijelölés gombot.** Értesítést kap arról, hogy egy kulcs jön létre a key vaultban.

    ![Új kulcs a key vaultban](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

A **Titkosítás** panelen láthatja a kulcstartót és az ügyfél által felügyelt kulcshoz kiválasztott kulcsot.

## <a name="disable-keys"></a>Billentyűk letiltása

Az importálási/exportálási feladat bármely szakaszában letilthatja a Microsoft által kezelt kulcsokat, és áthelyezheti az ügyfél által kezelt kulcsokat. Létrehozása után azonban nem tilthatja le az ügyfél által kezelt kulcsot.

## <a name="troubleshoot-customer-managed-key-errors"></a>Ügyfél által kezelt kulcshibák elhárítása

Ha az ügyfél által kezelt kulccsal kapcsolatos hibákat észlel, az alábbi táblázat segítségével háríthatja el a hibákat:

| Hibakód     |Részletek     | Visszaszerezhető?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Alkalmazott egy ügyfél által kezelt kulcsot, de a kulcshozzáférés jelenleg visszavonásra kerül. További információt [a kulcshozzáférés engedélyezése című témakörben talál.](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)                                                      | Igen, ellenőrizze, hogy: <ol><li>A key vault továbbra is rendelkezik az MSI a hozzáférési szabályzatban.</li><li>A hozzáférési házirend engedélyeket biztosít a Beszedés, Betakarás és Kicsomagolás lehetőséghez.</li><li>Ha a kulcstartó a tűzfal mögötti virtuális hálózatban van, ellenőrizze, hogy **engedélyezze-e** a Microsoft megbízható szolgáltatások engedélyezését.</li></ol>                                                                                            |
| CmkError Disabled      | Alkalmazott egy ügyfél által kezelt kulcsot, de a kulcs le van tiltva. További információt [a kulcs engedélyezése című témakörben talál.](https://docs.microsoft.com/rest/api/keyvault/vaults/createorupdate)                                                                             | Igen, a kulcsverzió engedélyezésével     |
| CmkErrorNemTalálható      | Alkalmazott egy ügyfél által kezelt kulcsot, de nem találja a kulcsot. <br>Ha a kulcs törlődik, és a megőrzési időszak után törlődik, nem tudja helyreállítani a kulcsot. Ha biztonsági másolatot tett a kulcsról, a probléma megoldásához visszaállíthatja a kulcsot. | Nem, a kulcs törölve lett, és a megőrzési időszak után is törlődött. <br>Igen, csak akkor, ha az ügyfél rendelkezik a kulccsal, és visszaállítja azt.  |
| CmkErrorVaultNem található | Alkalmazott egy ügyfél által kezelt kulcsot, de nem találja a kulcshoz társított kulcstartót.<br>Ha törölte a key vault, nem tudja helyreállítani az ügyfél által kezelt kulcs.  Ha áttelepítette a key vault egy másik bérlő, [lásd: Kulcstartó bérlői azonosító módosítása előfizetés áthelyezése után.](https://docs.microsoft.com/azure/key-vault/key-vault-subscription-move-fix) |   Nem, ha az ügyfél törölte a key vault.<br> Igen, ha a kulcstartó bérlői áttelepítésen ment keresztül, tegye a következők egyikét: <ol><li>helyezze vissza a kulcstartót a régi bérlőhöz.</li><li>set Identity = None, majd vissza az Identity = SystemAssigned, ez törli, és újralétrehozza az identitást</li></ol>|

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
