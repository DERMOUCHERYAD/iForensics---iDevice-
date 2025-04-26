```markdown
# FCSC 2025 Forensics: iOS Backup Metadata

## Description
Challenge Forensics / Systèmes

À la douane, votre téléphone iOS a été confisqué puis rendu. Vous recevez un **sysdiagnose** et un **backup iTunes** non chiffré. Le flag n'est pas dans un binaire obscur, mais dans les métadonnées du backup iOS.

**Objectif** : extraire le modèle et la version de build d’iOS depuis `Info.plist` pour reconstituer le flag :
```
FCSC{<ProductType>|<BuildVersion>}
```

Par exemple : `FCSC{iPhone12,3|20A362}`.

---

## Fichiers fournis

- `backup.tar.xz` : archive contenant l’arborescence du backup iTunes.
- `sysdiagnose_and_crashes.tar.xz` : dump système complet (optionnel pour ce challenge).

---

## Étapes d’exploitation

1. **Extraire l’archive** :
   ```bash
   tar -xJvf backup.tar.xz
   ```

2. **Localiser `Info.plist`** :
   ```bash
   find . -type f -name "Info.plist"
   ```

3. **Lire le PLIST binaire** : sans dépendance externe, on utilise Python :
   ```bash
eval "$(cat << 'EOF'
import plistlib, pprint
with open('Info.plist', 'rb') as f:
    data = plistlib.load(f)
pprint.pprint(data)
EOF
)" | python3 -
   ```
   Ou plus simplement :
   ```bash
   python3 - << 'EOF'
import plistlib, pprint
with open('Info.plist','rb') as f:
    data = plistlib.load(f)
pprint.pprint(data)
EOF
   ```

4. **Extraire les clés** :
   - `data['ProductType']` → modèle matériel (ex. `iPhone12,3`)
   - `data['BuildVersion']` → version de build iOS (ex. `20A362`)

5. **Assembler le flag** :
```
FCSC{iPhone12,3|20A362}
```

---

## Outils recommandés

- **Python 3** (module `plistlib`, `pprint` inclus)
- `tar` (GNU tar pour .xz)
- `find` (pour localiser les fichiers)

---

## Remarques

- Aucun outil externe (plutil, xmllint) n’est nécessaire : tout se fait en Python.
- Le backup iOS contient de nombreuses infos sensibles : vigilance en situation réelle.

```

