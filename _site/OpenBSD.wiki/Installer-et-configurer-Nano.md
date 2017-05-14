# Installer et configurer Nano

Tout d'abords, installons Nano.

> pkg_add nano

Nous cherchons ensuite où est caché Nano.

> find / -name "nano"

```
# find / -name "nano"
/usr/local/bin/nano
/usr/local/share/doc/nano
/usr/local/share/examples/nano
/usr/local/share/nano
```

Puis copions un exemple de fichier de configure dans le répertoire home de l'utilisateur souhaité.

> cp /usr/local/share/examples/nano/nanorc.sample ~/.nanorc

Et enfin nous éditions ce fameux fichier de configuration.

> nano ~/.nanorc

Dans ce fichier, nous pouvons par exemple activer les choses suivantes.

Activer l'auto-indentation avec `set autoindent`.

```
## Use auto-indentation.
set autoindent
```

Activer la souris en mode console dans Nano (si disponible par l'OS) avec `set mouse`.

```
## Enable mouse support, if available for your system.  When enabled,
## mouse clicks can be used to place the cursor, set the mark (with a
## double click), and execute shortcuts.  The mouse will work in the X
## Window System, and on the console when gpm is running.
set mouse
```

Puis on active la coloration syntaxique avec `include "/usr/local/share/nano/*.nanorc"`. Cette option est géniale car elle permet d'inclure toutes les colorations en une seule fois.

```
## to set the background color to black or white.
##
## All regexes should be extended regular expressions.
##
## If you wish, you may put your syntax definitions in separate files.
## You can make use of such files as follows:
##
## include "/path/to/syntax_file.nanorc"
##
## Unless otherwise noted, the name of the syntax file (without the
## ".nanorc" extension) should be the same as the "short description"
## name inside that file.  These names are kept fairly short to make
## them easier to remember and faster to type using nano's -Y option.
##
## To include all existing syntax definitions, you can do:
include "/usr/local/share/nano/*.nanorc"
```

Et on enregistre avec la combinaison de touche `CTRL + X` et on répond `Y`.

Pour afficher le nombre de ligne dans Nano, on utilise la commande suivante `nano -c`.

> nano -c ~/.nanorc