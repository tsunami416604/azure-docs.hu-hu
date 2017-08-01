Az AzureRm.Resources modul 3.0-s verziója jelentős változásokat hozott a címkék használata terén. A továbblépés előtt ellenőrizze, milyen verziót használ:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Ha 3.0-s vagy annál újabb verziót, a témakörben szereplő példák alkalmazhatók az Ön által használt környezetre. Ha a 3.0-nál régebbi verzióval rendelkezik, először [frissítsen egy újabb verzióra](/powershell/azureps-cmdlets-docs/) a PowerShell-galériával vagy a Webplatform-telepítővel, és csak ezt követően lépjen tovább.

```powershell
Version
-------
3.5.0
```

*Erőforráscsoportok* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

A szkript a következő formátumot adja vissza:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Vagy *megadott névvel és erőforráscsoporttal rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*Adott címkével rendelkező erőforráscsoportok* lekéréséhez használja a következőt:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

*Adott címkével rendelkező erőforrások* lekéréséhez használja a következőt:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Ezért különböző megközelítéseket kell alkalmaznia annak függvényében, hogy az adott erőforrás vagy erőforráscsoport rendelkezik-e címkével. 

Ha *meglévő címkék nélküli erőforráscsoporthoz* szeretne címkéket adni, használja a következőt:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Ha *meglévő címkékkel rendelkező erőforráscsoporthoz* szeretne címkéket adni, kérje le a meglévő címkéket, adja hozzá az új címkét, és alkalmazza ismét a címkéket:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Ha *meglévő címkék nélküli erőforráshoz* szeretne címkéket adni, használja a következőt:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName examplegroup
```

Ha *meglévő címkékkel rendelkező erőforráshoz* szeretne címkéket adni, használja a következőt:

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
```

Ha az erőforráscsoport összes címkéjét szeretné alkalmazni a csoport erőforrásaira, és *nem szeretné megőrizni az erőforrások meglévő címkéit*, használja a következő szkriptet:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Ha az erőforráscsoport összes címkéjét szeretné alkalmazni a csoport erőforrásaira, és *nem szeretné megőrizni az erőforrások meglévő, nem ismétlődő címkéit*, a következő szkriptet kell használnia:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Az összes címke eltávolításához adjon át egy üres kivonattáblát:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



