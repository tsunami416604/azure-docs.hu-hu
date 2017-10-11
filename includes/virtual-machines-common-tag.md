


## <a name="tagging-a-virtual-machine-through-templates"></a>A sablonok használatával virtuálisgép-címkézés
Első lépésként nézzük címkézés sablonok keresztül. [Ez a sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) címkék helyez el a következőket: számítási (virtuális gép), a tároló (Tárfiók) és a hálózaton (a nyilvános IP-cím, a virtuális hálózati és a hálózati adapter). Ez a sablon a Windows virtuális gépek azonban Linux virtuális gépek módosítani kell.

Kattintson a **az Azure telepítéséhez** gombot a [Sablonhivatkozás](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Ez jelenik meg, hogy a [Azure-portálon](https://portal.azure.com/) sablon telepíthető.

![Egyszerű telepítés címkékkel](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Ez a sablon tartalmazza a következő címkékkel: *részleg*, *alkalmazás*, és *létrehozta*. Akkor is felvehet és szerkeszthet ezekkel a címkékkel, közvetlenül a sablonban, ha azt szeretné, hogy különböző címkenevek.

![Egy Azure címkék](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Ahogy látja, a címkék is meg van adva kulcs/érték párok, egymástól kettősponttal (:). A címkék a következő formátumban kell megadni:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

A sablonfájl mentési a címkékkel, az Ön által választott a Szerkesztés befejezése után.

Ezután a **paraméterek szerkesztése** szakaszban kitöltheti az értékeket a címkék.

![Címkék szerkesztése az Azure-portálon](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Kattintson a **létrehozása** a címke értékekkel a sablon telepítéséhez.

## <a name="tagging-through-the-portal"></a>A portálon keresztül címkézés
Miután létrehozta az erőforrások címkékkel, megtekintheti, vegye fel, és a portál címkék törlése.

Válassza ki a címkék ikonra a címkék megtekintéséhez:

![Azure-portálon címkék ikon](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

A portálon keresztül új címke hozzáadása a saját kulcs/érték pár meghatározásával, és mentse.

![Új címke hozzáadása az Azure-portálon](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Az új címke ekkor meg kell jelennie az erőforrás címkék listájában.

![Új címke az Azure portálon mentése](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

