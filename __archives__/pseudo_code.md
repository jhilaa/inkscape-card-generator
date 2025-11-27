# Constantes
FONT_PATH : 
FONT_FAMILY : 
FONT_SIZE : 
TITLE_SLOT_ID : 
IMAGE_SLOT_ID : 
TEXT1_SLOT_ID : 
TEXT2_SLOT_ID : 
WORDS_AND_FORMULA_TRIM_PATTERN :   # regex pour découper une chaine de caractère en séparant pour isolé chque mot et chaque expression latex

# Options pour affiner le positionnement entre autres si besoin
FONT_PATH: "C:\\Windows\\Fonts\\arial.ttf"
FONT_FAMILY: "Arial"
FONT_SIZE: 28
LINE_GAP: 2
OVERFLOW: ellipsis

# Réglages pour les formules LaTeX
latex_block_em: 2.3      # ↑ plus grand que 1.6
latex_inline_em: 1.25     # inline mieux intégré
baseline_ratio: 0.82      # alignement vertical plus juste
latex_scale: 2.5

#FONCTIONS
getParam (param) 
	# Renvoie la valeur du paramètre param à partir du fichier config.yml

setTitle (title)
	# Ecris le titre title dans le bloc TITLE_SLOT_ID
	
setImage (image_url)
	# Met à jour le bloc IMAGE_SLOT_ID pour afficher l'image à partir de image_url
	
getTextWidth (text) 
	# Renvoie la longueur de la chaine de caractère text en fonction de la police et de taille de la police
	
getPngFromLatex (latexFormula)
	# Renvoie le code correspondant à l'image de l'équation correspondant à la formule latexFormula
	
getPngWith (png)
	# Renvoie la largeur d'une image
	
getWordsAndFormulasList (text) 
	# Renvoie une liste ordonnée des mots et des formules dans text à partir de la regex WORDS_AND_FORMULA_TRIM_PATTERN sous forme de liste d'objets {type (WORD ou FORMULA), string (chaine de caractères ou png))
	
putElementInSlot (element, type)
	# positionne l'élement
	
SetTextInSlot (slotId, text)
	# Met à jour le slot passé en paramètre pour afficher le texte text (qui peut contenit du latex
	words_and_formulas_list = getWordsAndFormulasList (text)
	slot_width = getSlotWidth (text_slot)
	slot_height = getSlotHeight (text_slot)
	cursorPosition = 0 # ou autre valeur de référence pour le début de ligne
	# On parcourt le tableau de éléments à ajouter dans le bloc et on teste le type et la longueur pour s'avoir comment pour contruire élement par élément le texte à insérer dans le slot, en tenant compte la largeur du slot
	# pour tout élement element de words_and_formulas_list
	#	si element.type = TEXT 
	#		alors element_width = getTextWidth (element.string)
	#	si element.type = FORMULA 
	#		alors png = getPngFromLatex (element.string) et element_width = getPngWith (png)
	#	si temp_line_width + element_width > slot_width
	#		alors on passe à la ligne et cursorPosition = 0			
	#	on ajoute l'element dans le slot à la suite des autres et on déplace le curseur pour le prochaine 
	#	putElementInSlot (element, type) 
	#	cursorPosition = cursorPosition + element_width
				