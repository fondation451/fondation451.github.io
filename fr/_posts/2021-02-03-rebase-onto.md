Rebase en cascade facile

Ici, on va considérer que l'on a trois branches `A`, `B` et `C`. La branche principale est `dev`.
On nomme `cx` un commit de la branche `X`.

```
dev                A         B                  C
   --ca1--ca2--ca3--cb1--cb2--cc1--cc2--cc3--cc4
```

Donc ici, si on considère les branches séparément, on a

`A = ...dev--ca1--ca2--ca3`
`B = ...dev--ca1--ca2--ca3--cb1--cb2`
`C = ...dev--ca1--ca2--ca3--cb1--cb2--cc1--cc2--cc3--cc4`

Disons que le travail est terminé et que l'on veux fusionner ces trois branches dans des PRs différentes.

Entre temps, `dev` a avancé à `dev'`. Donc il faut mettre à jour notre travail.

Tout d'abord, on fusionne la première `A`. C'est facile.
On met à jour (fusion avec `dev`) puis on fusionne (fusion dans `dev`)
`git co A`
`git merge dev`
`git co dev`
`git merge A`

On a maintenant `dev` qui inclue les modifications de `A`, nous l'appellerons `dev''`

Puis on va "rebaser" la branche `B` pour pouvoir faire la même chose.
Les commits `ca1--ca2--ca3` sont déjà fusionner dans `dev`. Ceux qui nous intéressent, ce sont les commits `cb1--cb2`.
Pour rappelle, on a `B = ...dev--ca1--ca2--ca3--cb1--cb2`
On va donc faire un rebase "onto" pour ne récupérer que `cb1--cb2` et les mettre sur `dev''`. Puis on aura plus qu'à fusionner la branche dans `dev`.
`git co B`
`git rebase --onto dev ca3`
`git co dev`
`git merge B`

On a maintenant `dev` qui inclue les modifications de `A` et de `B`, nous l'appellerons `dev'''`

La démarche pour `C` est similaire à celle pour `B`.
Les commits `ca1--ca2--ca3--cb1--cb2` sont déjà fusionner dans `dev`. Ceux qui nous intéressent, ce sont les commits `cc1--cc2--cc3--cc4`.
Pour rappelle, on a `C = ...dev--ca1--ca2--ca3--cb1--cb2--cc1--cc2--cc3--cc4`
On va donc faire un rebase "onto" pour ne récupérer que `cc1--cc2--cc3--cc4` et les mettre sur `dev'''`. Puis on aura plus qu'à fusionner la branche dans `dev`.
`git co C`
`git rebase --onto dev cb2`
`git co dev`
`git merge C`

Voilà




NOTE :
Quand on est sur la branche `X`, la commande `git rebase --onto dev commit` va simplement créer une nouvelle branche `X` qui se trouvera sur `dev` et qui inclurera par dessus tout les les commits de la branche X entre commit et la tête de la branche.
