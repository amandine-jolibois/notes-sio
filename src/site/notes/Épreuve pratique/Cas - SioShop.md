---
{"dg-home":false,"dg-publish":true,"permalink":"/epreuve-pratique/cas-sio-shop/","dgPassFrontmatter":true}
---

- [[Accueil\|Accueil]]

# Mission A :
#MissionA

![Pasted image 20230407142755.png](/img/user/Pasted%20image%2020230407142755.png)

## Modifications de la base de données 

>Ajout de la table catégorie : 

![Pasted image 20230407142917.png](/img/user/Pasted%20image%2020230407142917.png)

>Ajout de la colonne catégorie dans la table produit, clé étrangère a catégorie :

```SQL
ALTER TABLE produit ADD categorie INT;
ALTER TABLE produit ADD CONSTRAINT FOREIGN KEY (categorie)
REFERENCES categorie(reference);
```

>Ajout des données dans la base.


## Modification de la class produit

>Ajout des deux variables, référence et libelle, variables, constructeur et getters : 

```C#
private int refCat;
private string libelle;

refCat = uneRefCat;
libelle = unLibelle;

int indiceRefCat = reader.GetOrdinal("reference");
int indiceLibelle = reader.GetOrdinal("libelle");

refCat = int.Parse(reader.GetString(indiceRefCat));
libelle = reader.GetString(indiceLibelle);

 public int GetRefCat()
        {
            return refCat;
        }

        public string GetLibelle()
        {
            return libelle;
        }
```




## Modifications fm_produit / fm_detailClient

>Ajout de la colonne catégorie dans la listeView 

>Ajout de l'affichage des catégories dans la liste (jointures) : 

```C#
 sql = $"SELECT * FROM produit JOIN categorie ON produit.categorie=categorie.reference WHERE marque = '{uneMarque}' ORDER BY marque, nom;";

string[] ligne = { unProduit.GetReference().ToString(), unProduit.GetMarque(), unProduit.GetLibelle(),unProduit.GetNom(), unProduit.GetPrix().ToString(), unProduit.GetStock().ToString() };
```

















## Modification de fm_detailProduit

>Ajout du textBox en ReadOnly 

```C#
tb_categorie.Text = unProduit.GetLibelle();
```

# Mission B 
#MissionB

![Pasted image 20230407143812.png](/img/user/Pasted%20image%2020230407143812.png)

## Modification de fm_produit 

>Ajout du bouton majStock 
>Visible uniquement si c'est l'admin de connecter : 

```C#
if (Program.employe.GetLogin() == "admin")
            {
                bt_majStock.Visible = true;
            }
            else
            {
                bt_majStock.Visible = false;
            }
```

>Lecture du fichier CSV ligne par ligne et requête UPDATE pour ajouter la livraison au stock :

```C#
try
            {
                string sql;
                string path_csv = @"D:\Entrainement SioShop5\data_csv\livraison_22122021.csv"; //chemin du fichier CSV
                string[] lignes = File.ReadAllLines(path_csv);  //chaque ligne du tableau contient une ligne du fichier
                for (int i = 0; i < lignes.Length; i++) //on parcourt le tableau
                {
                    string[] parts = lignes[i].Split(';'); //on sépare chaque élément avec le ;

                    sql = $"UPDATE produit SET stock = stock + {parts[1]} WHERE reference = {parts[0]}";

                MySqlCommand command = new MySqlCommand();
                command.Connection = Program.ConnexionBdd;
                command.CommandText = sql;

                int result = command.ExecuteNonQuery();
                  
            }
                MessageBox.Show("Mise à jour éffectuée ! ");

                listViewProduit.Items.Clear();
                string marque = "TOUTES";
                AfficheProduit(marque);
            }
            catch (Exception ex) 
            { 
                MessageBox.Show("Erreur : " + ex.Message);
            }

            }
```

>Mise à jour de la listeView : 

```C#
listViewProduit.Items.Clear();
string marque = "TOUTES";
AfficheProduit(marque);
```

>MessageBox pour indiquer l'état de l'opération : 

```C#
MessageBox.Show("Mise à jour éffectuée ! ");

MessageBox.Show("Erreur : " + ex.Message);
```