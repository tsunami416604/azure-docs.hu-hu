A jelen cikk példái a 3.0-s vagy újabb, az Azure PowerShell igényelnek. Ha nem rendelkezik 3.0-s vagy újabb, [frissítenie](/powershell/azureps-cmdlets-docs/) PowerShell-galériában vagy a Webplatform-telepítő használatával.

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
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Ha *meglévő címkékkel rendelkező erőforráshoz* szeretne címkéket adni, használja a következőt:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.tags += @{Status="Approved"}
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
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
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Az összes címke eltávolításához adjon át egy üres kivonattáblát:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```
