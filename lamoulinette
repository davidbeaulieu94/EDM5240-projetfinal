# coding: utf-8
# 2017 Jean-Hugues Roy, GNU GPL v3.

import csv, os, glob, json, requests

# On commence par lire le plus récent fichier CSV dans le répertoire où on se trouve
csvRecent = max(glob.iglob("*.[Cc][Ss][Vv]"), key=os.path.getmtime)
print(csvRecent)

fichier1 = ""

# On s'assure ensuite que c'est bel et bien le bon fichier
while fichier1 == "":

	print("\n" +"*"*20 + " QUESTION " + "*"*20)
	print("Le fichier que vous souhaitez géocoder est-il '{}'?".format(csvRecent))
	print("*"*50)
	q1 = str(input("['o'|'n'] "))

	if q1 == "o":
		fichier1 = csvRecent
	elif q1 == "n":
		q2 = ""
		print("\n" +"*"*20 + " QUESTION " + "*"*20)
		print("Alors quel est le nom du fichier que vous souhaitez géocoder?")
		print("*"*50)
		q2 = str(input("Entrez le nom ici -> "))
		if q2[-4:] == ".csv":
			fichier1 = q2
		else:
			while q2[-4:] != ".csv":
				print("\n" +"*"*20 + " QUESTION " + "*"*20)
				print("Veuillez entrer un nom de fichier valide (se terminant par '.csv').")
				print("*"*50)
				q2 = str(input("Par ici svp -> "))
				if q2[-4:] == ".csv":
					fichier1 = q2
	else:
		print("\nNe pas répondre à mes questions provoque en moi un drame existentiel... et je cesse ainsi d'exister")

# On baptise notre fichier de «output» qui contiendra nos résultats
fichier2 = "{}-geoocode.csv".format(fichier1[:-4])

# On compte le nombre de lignes du fichier
nblignes = len(open(fichier1).readlines())

# On le lit à nouveau
f1 = open(fichier1)
geo = csv.reader(f1)

# On va lire les entêtes du fichier
n = 0
for ligne in geo:
	while n == 0:
		entetes = ligne
		n += 1

# On propose d'afficher leurs noms
print("_"*80)
print("\n")
print("Vous avez {} colonnes dans votre fichier.".format(str(len(entetes))))
print("Voulez-vous que j'affiche leurs noms?")
q3 = str(input("['o'|'n'] "))

if q3 == "o":

	n = 0
	print("_"*80)
	print("\n")
	for colonne in entetes:
		print("L'index de la colonne '{}' est : {}".format(colonne,n))
		n += 1

q4 = ""

# On demande ensuite quelles colonnes vont servir à construire une adresse la plus complète possible
while q4 != "o":

	print("\nEntrez tous les numéros d'index de colonnes qui pourraient m'être utiles pour géocoder vos points\n(adresses, codes postaux, noms de villes, de provinces, de pays)")
	col = str(input("Entrez autant de numéros que nécessaire sur la même ligne, chacun étant séparé par une virgule -> "))
	colonnes = col.split(",")

	print("\n")
	print("Je vais utiliser les colonnes suivantes :")

	for index in colonnes:
		print("   >>> " + entetes[int(index)])

	print("\n")
	print("C'est beau? On peut procéder?")
	q4 = str(input("['o'|'n'] "))

f1 = open(fichier1)
geo = csv.reader(f1)
next(geo)

print("\n")

n = 0

# On lit ensuite chacune des lignes de notre fichier pour aller chercher les colonnes que l'utilisateur a identifiées
for ligne in geo:
	n += 1
	adresse = ""
	for index in colonnes:
		# print(index)
		adresse = adresse + " " + ligne[int(index)]

# On utilise l'API de Google Maps pour géocoder l'adresse (en trouver les coordonnées)
		url = "https://maps.googleapis.com/maps/api/geocode/json?address=" + adresse
		req = requests.get(url)
		google = req.json()
		if len(google["results"]) != 0:
			latitude = google["results"][0]["geometry"]["location"]["lat"]
			longitude = google["results"][0]["geometry"]["location"]["lng"]
		else:
			latitude = "?"
			longitude = "?"

# On ajoute les coordonnées à chaque ligne du fichier
	ligne.append(latitude)
	ligne.append(longitude)

	print("Les coordonnées du point {} ({}) sur {} sont ({},{})".format(n,adresse,nblignes,latitude,longitude))

# On écrit notre nouveau fichier une ligne à la fois
	yo = open(fichier2,"a")
	yolo = csv.writer(yo)
	yolo.writerow(ligne)
