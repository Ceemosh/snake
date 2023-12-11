import sys
import random
import pygame

pygame.init()

largeur, hauteur = 640, 480
taille_case = 20
vitesse = 10

couleur_fond = (4, 4, 4)
couleur_serpent = (0, 255, 0)
couleur_pomme = (255, 0, 0)

class Serpent:
    def __init__(self):
        self.longueur = 1
       
        self.corps = [(largeur // 2, hauteur // 2)]
        self.direction = (1, 0)
        self.carrés_rouges_mangés = 0  # Ajout du compteur

    def deplacer(self):
        tete_x, tete_y = self.corps[0]
        dx, dy = self.direction
        nouvelle_tete = ((tete_x + dx * taille_case) % largeur, (tete_y + dy * taille_case) % hauteur)
        self.corps.insert(0, nouvelle_tete)
        if len(self.corps) > self.longueur:
            self.corps.pop()

    def manger_pomme(self):
        self.longueur += 1
        
        self.carrés_rouges_mangés += 1  # Augmentation du compteur lors de la consommation d'une pomme

    def collision(self):
        return self.corps[0] in self.corps[1:]

class Pomme:
    def __init__(self):
        self.position = (random.randint(0, (largeur - taille_case) // taille_case) * taille_case,
                         random.randint(0, (hauteur - taille_case) // taille_case) * taille_case)

    def placer(self):
        self.position = (random.randint(0, (largeur - taille_case) // taille_case) * taille_case,
                         random.randint(0, (hauteur - taille_case) // taille_case) * taille_case)

fenetre = pygame.display.set_mode((largeur, hauteur))
pygame.display.set_caption("Jeu de Serpent")

serpent = Serpent()
pomme = Pomme()

clock = pygame.time.Clock()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP and serpent.direction != (0, 1):
                serpent.direction = (0, -1)
            elif event.key == pygame.K_DOWN and serpent.direction != (0, -1):
                serpent.direction = (0, 1)
            elif event.key == pygame.K_LEFT and serpent.direction != (1, 0):
                serpent.direction = (-1, 0)
            elif event.key == pygame.K_RIGHT and serpent.direction != (-1, 0):
                serpent.direction = (1, 0)

    serpent.deplacer()

    if serpent.corps[0] == pomme.position:
        serpent.manger_pomme()
        pomme.placer()

    if serpent.collision():
        pygame.quit()
        sys.exit()

    fenetre.fill(couleur_fond)

    for segment in serpent.corps:
        pygame.draw.rect(fenetre, couleur_serpent, (segment[0], segment[1], taille_case, taille_case))

    pygame.draw.rect(fenetre, couleur_pomme, (pomme.position[0], pomme.position[1], taille_case, taille_case))

    # Afficher le nombre de carrés rouges mangés à l'écran
    font = pygame.font.Font(None, 36)
    text = font.render(f"Points : {serpent.carrés_rouges_mangés}", True, (255, 255, 255))
    fenetre.blit(text, (10, 10))

    pygame.display.flip()

    clock.tick(vitesse)
