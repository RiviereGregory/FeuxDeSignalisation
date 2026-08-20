**Guide de fabrication PCB sous Fusion 360 (CAM vers GRBL / Candle)**

---

**1. Préparation du modèle 3D et du Posage (*Setup*)**

* **Génération du 3D :** Dans l'environnement PCB 2D, cliquer sur « Pousser vers le PCB 3D ».
* **Espace de travail :** Basculer le menu principal de **CONCEPTION** (ou *CCI 3D*) vers **FABRICATION** $\rightarrow$ onglet **FRAISAGE**.
* **Masquage des composants :** Dans le navigateur à gauche, masquer le dossier `Packages` (icône œil) pour ne conserver que la plaque FR4 et les pistes en cuivre.
* **Création du Posage (*Setup*) :**
* **Type d'opération :** Fraisage.
* **Modèle :** Sélectionner le corps 3D de la plaque FR4 (vérifier que l'épaisseur $Z$ indique bien $1,50\text{ mm}$).
* **Système d'axes (WCS) :** `Axe/Plan Z et axe X`. Placer l'origine $(0,0,0)$ sur le **coin inférieur gauche de la face cuivre**.
* **Orientation Z :** L'axe $Z$ (flèche bleue) doit **pointer vers le haut** (sortir de la face cuivre, vers la broche).
* **Brut :** *Bloc à taille relative* avec tous les décalages à $0,00\text{ mm}$ ($60 \times 40 \times 1,5\text{ mm}$).



---

**2. Opération 1 — Isolation des pistes (Trace 2D)**

* **Stratégie :** Menu **2D** $\rightarrow$ **Trace**.
* **Outil :** Fraise conique V-Bit / Javelot ($\varnothing \text{ pointe } 0,1\text{ mm}$, angle $20^\circ$ ou $30^\circ$, tige $3,175\text{ mm}$).
* **Données de coupe :**
* Vitesse de broche : $10\ 000\text{ tr/min}$
* Avance de coupe (XY) : $150\text{ mm/min}$
* Avance de plongée (Z) : $60\text{ mm/min}$


* **Géométrie :** Sélectionner les arêtes extérieures des pistes sur la face cuivre (ou le calque d'esquisse).
* **Hauteurs :**
* Dégagement : $4,00\text{ mm}$
* Retrait : $2,00\text{ mm}$ (depuis *Haut du brut*)
* Avance : $1,00\text{ mm}$ (depuis *Haut du brut*)


* **Passes :**
* Répéter les passes / Compensation : `Centre`
* **Décalage en Z :** **`-0,06 mm`** (pour entailler la couche de cuivre de $35\text{ }\mu\text{m}$).



---

**3. Opération 2 — Perçage THT (Perçage)**

* **Stratégie :** Menu **PERÇAGE** $\rightarrow$ **Perçage**.
* **Outil :** Foret $\varnothing 1,0\text{ mm}$.
* **Données de coupe :** Broche à $10\ 000\text{ tr/min}$, plongée Z à $60\text{ mm/min}$.
* **Géométrie :** Cocher **Sélectionner le même diamètre** et cliquer sur un trou intérieur de la carte.
* **Hauteurs :**
* **Hauteur du bas :** *Bas du trou* (*Hole Bottom*).
* **Décalage :** **`-0,3 mm`** (pour déboucher proprement à $Z = -1,8\text{ mm}$ dans le martyr).



---

**4. Opération 3 — Détourage de la carte (Contour 2D)**

* **Stratégie :** Menu **2D** $\rightarrow$ **Contour 2D**.
* **Outil :** Fraise droite ou râpe ($\varnothing 1,5\text{ mm}$ ou $2,0\text{ mm}$).
* **Données de coupe :** Broche à $10\ 000\text{ tr/min}$, avance XY à $100\text{ mm/min}$, plongée Z à $40\text{ mm/min}$.
* **Géométrie :**
* Sélectionner l'arête inférieure du contour extérieur de la plaque.
* Cocher **Onglets** (*Tabs*) : Largeur $1,5\text{ mm}$, Hauteur $1,0\text{ mm}$ (positionnement automatique à des distances).


* **Hauteurs :**
* **Hauteur du bas :** *Bas du brut* (*Stock Bottom*).
* **Décalage :** **`-0,2 mm`** (pour couper la plaque à $Z = -1,7\text{ mm}$).


* **Passes :**
* Cocher **Profondeurs multiples**.
* **Profondeur de passe maximale :** **`0,3 mm`** (découpe progressive en 5 à 6 passes).



---

**5. Export du G-Code et Exécution sous Candle**

* **Post-processeur :** Utiliser exclusivement **`grbl.cps`**.
* **Génération séparée (1 fichier par outil) :**
1. Clic droit sur `Trace1` $\rightarrow$ Post-traiter $\rightarrow$ **`1_gravure.nc`**
2. Clic droit sur `Percer1` $\rightarrow$ Post-traiter $\rightarrow$ **`2_percage.nc`**
3. Clic droit sur `Contour 2D1` $\rightarrow$ Post-traiter $\rightarrow$ **`3_detourage.nc`**


* **Procédure d'usinage dans Candle :**
1. Fixer la plaque brute sur le martyr au scotch double-face.
2. Charger `1_gravure.nc` $\rightarrow$ Monter la fraise V-Bit $\rightarrow$ Faire le **Zero XY** et le **Zero Z** (palpage) $\rightarrow$ Lancer le travail.
3. Charger `2_percage.nc` $\rightarrow$ Monter le foret $\varnothing 1\text{ mm}$ $\rightarrow$ **Conserver l'origine XY** $\rightarrow$ Refaire le **Zero Z** uniquement $\rightarrow$ Lancer.
4. Charger `3_detourage.nc` $\rightarrow$ Monter la fraise $\varnothing 1,5\text{ mm}$ $\rightarrow$ **Conserver l'origine XY** $\rightarrow$ Refaire le **Zero Z** uniquement $\rightarrow$ Lancer.
5. Décoller la carte et araser les 4 pontets au cutter.