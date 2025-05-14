#include "Menu.h"
#include <stdio.h>
#include <stdlib.h>

Menu init_Menu(SDL_Surface* screen) {
    Menu m;
    { SDL_Surface* tmp = IMG_Load("backgg1.png"); m.image_bg = SDL_DisplayFormat(tmp); SDL_FreeSurface(tmp); }
    m.pos_bg.x = 0; m.pos_bg.y = 0;
    m.nbr_boutton = 3;
    
    // Chargement des sons
    m.hover_sound = Mix_LoadWAV("button_hover.wav");
    m.click_sound = Mix_LoadWAV("button_click.wav");
    
    // Bouton 1: New Game
    { 
        SDL_Surface* tmp = IMG_Load("np.png"); 
        m.tab[0].Img[0] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp); 
        tmp = IMG_Load("npHover.png");
        m.tab[0].Img[1] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp);
    }
    m.tab[0].Pos.x = (screen->w - 174) / 2;
    m.tab[0].Pos.y = 300;
    m.tab[0].num = 1;
    m.tab[0].son = NULL;
    // Bouton 2: Load Game
    { 
        SDL_Surface* tmp = IMG_Load("changer jeu.png"); 
        m.tab[1].Img[0] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp); 
        tmp = IMG_Load("changer jeuHover.png");
        m.tab[1].Img[1] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp);
    }
    m.tab[1].Pos.x = (screen->w - 174) / 2;
    m.tab[1].Pos.y = 400;
    m.tab[1].num = 2;
    m.tab[1].son = NULL;
    // Bouton 3: Exit
    { 
        SDL_Surface* tmp = IMG_Load("non.png"); 
        m.tab[2].Img[0] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp); 
        tmp = IMG_Load("nonHover.png");
        m.tab[2].Img[1] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp);
    }
    m.tab[2].Pos.x = (screen->w - 174) / 2;
    m.tab[2].Pos.y = 500;
    m.tab[2].num = 3;
    m.tab[2].son = NULL;
    m.musique = NULL;
    m.num_bt_actif = 1;
    return m;
}

void afficher_Menu(Menu m, SDL_Surface* screen) {
    SDL_BlitSurface(m.image_bg, NULL, screen, &m.pos_bg);
    for(int i = 0; i < m.nbr_boutton; i++) {
        int idx = (m.num_bt_actif == m.tab[i].num) ? 1 : 0;
        SDL_BlitSurface(m.tab[i].Img[idx], NULL, screen, &m.tab[i].Pos);
    }
}

int update_Menu(Menu* m, SDL_Event event) {
    if(event.type == SDL_KEYDOWN) {
        switch(event.key.keysym.sym) {
            case SDLK_DOWN:
                if(m->num_bt_actif < m->nbr_boutton) m->num_bt_actif++;
                break;
            case SDLK_UP:
                if(m->num_bt_actif > 1) m->num_bt_actif--;
                break;
            case SDLK_RETURN:
                Mix_PlayChannel(-1, m->click_sound, 0);
                return m->num_bt_actif;
        }
    }
    else if(event.type == SDL_MOUSEMOTION) {
        for(int i = 0; i < m->nbr_boutton; i++) {
            SDL_Rect r = m->tab[i].Pos;
            r.w = m->tab[i].Img[0]->w;
            r.h = m->tab[i].Img[0]->h;
            if(event.motion.x >= r.x && event.motion.x <= r.x + r.w
            && event.motion.y >= r.y && event.motion.y <= r.y + r.h) {
                // Jouer le son de survol uniquement si on change de bouton
                if(m->num_bt_actif != m->tab[i].num) {
                    m->num_bt_actif = m->tab[i].num;
                    Mix_PlayChannel(-1, m->hover_sound, 0);
                }
            }
        }
    }
    else if(event.type == SDL_MOUSEBUTTONDOWN && event.button.button == SDL_BUTTON_LEFT) {
        for(int i = 0; i < m->nbr_boutton; i++) {
            SDL_Rect r = m->tab[i].Pos;
            r.w = m->tab[i].Img[0]->w;
            r.h = m->tab[i].Img[0]->h;
            if(event.button.x >= r.x && event.button.x <= r.x + r.w
            && event.button.y >= r.y && event.button.y <= r.y + r.h) {
                Mix_PlayChannel(-1, m->click_sound, 0);
                return m->tab[i].num;
            }
        }
    }
    return 0;
}

void liberer_Menu(Menu m) {
    SDL_FreeSurface(m.image_bg);
    for(int i = 0; i < m.nbr_boutton; i++) {
        SDL_FreeSurface(m.tab[i].Img[0]);
        SDL_FreeSurface(m.tab[i].Img[1]);
    }
    if(m.musique) Mix_FreeMusic(m.musique);
    Mix_FreeChunk(m.hover_sound);
    Mix_FreeChunk(m.click_sound);
}

// Sous-menu de sauvegarde (Save / Resume)
Menu init_SousMenu(SDL_Surface* screen) {
    Menu m;
    { SDL_Surface* tmp = IMG_Load("backgg1.png"); m.image_bg = SDL_DisplayFormat(tmp); SDL_FreeSurface(tmp); }
    m.pos_bg.x = 0; m.pos_bg.y = 0;
    m.nbr_boutton = 2;
    
    // Chargement des sons
    m.hover_sound = Mix_LoadWAV("button_hover.wav");
    m.click_sound = Mix_LoadWAV("button_click.wav");
    
    // Bouton 1: Save
    { 
        SDL_Surface* tmp = IMG_Load("oui.png"); 
        m.tab[0].Img[0] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp); 
        tmp = IMG_Load("ouiHover.png");
        m.tab[0].Img[1] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp);
    }
    m.tab[0].Pos.x = (screen->w - 174) / 2;
    m.tab[0].Pos.y = 350;
    m.tab[0].num = 1;
    m.tab[0].son = NULL;
    // Bouton 2: Resume
    { 
        SDL_Surface* tmp = IMG_Load("non.png"); 
        m.tab[1].Img[0] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp); 
        tmp = IMG_Load("nonHover.png");
        m.tab[1].Img[1] = SDL_DisplayFormatAlpha(tmp); 
        SDL_FreeSurface(tmp);
    }
    m.tab[1].Pos.x = (screen->w - 174) / 2;
    m.tab[1].Pos.y = 450;
    m.tab[1].num = 2;
    m.tab[1].son = NULL;
    m.musique = NULL;
    m.num_bt_actif = 1;
    return m;
}

void afficher_SousMenu(Menu sm, SDL_Surface* screen) {
    SDL_BlitSurface(sm.image_bg, NULL, screen, &sm.pos_bg);
    for(int i = 0; i < sm.nbr_boutton; i++) {
        int idx = (sm.num_bt_actif == sm.tab[i].num) ? 1 : 0;
        SDL_BlitSurface(sm.tab[i].Img[idx], NULL, screen, &sm.tab[i].Pos);
    }
}

int update_SousMenu(Menu* sm, SDL_Event event) {
    if(event.type == SDL_KEYDOWN) {
        switch(event.key.keysym.sym) {
            case SDLK_DOWN:
                if(sm->num_bt_actif < sm->nbr_boutton) sm->num_bt_actif++;
                break;
            case SDLK_UP:
                if(sm->num_bt_actif > 1) sm->num_bt_actif--;
                break;
            case SDLK_RETURN:
                // Save => -1, Resume => return to game (3)
                Mix_PlayChannel(-1, sm->click_sound, 0);
                if(sm->num_bt_actif == 1) return -1;
                else return 3;
        }
    }
    else if(event.type == SDL_MOUSEMOTION) {
        for(int i = 0; i < sm->nbr_boutton; i++) {
            SDL_Rect r = sm->tab[i].Pos;
            r.w = sm->tab[i].Img[0]->w;
            r.h = sm->tab[i].Img[0]->h;
            if(event.motion.x >= r.x && event.motion.x <= r.x + r.w
            && event.motion.y >= r.y && event.motion.y <= r.y + r.h) {
                // Jouer le son de survol uniquement si on change de bouton
                if(sm->num_bt_actif != sm->tab[i].num) {
                    sm->num_bt_actif = sm->tab[i].num;
                    Mix_PlayChannel(-1, sm->hover_sound, 0);
                }
            }
        }
    }
    else if(event.type == SDL_MOUSEBUTTONDOWN && event.button.button == SDL_BUTTON_LEFT) {
        for(int i = 0; i < sm->nbr_boutton; i++) {
            SDL_Rect r = sm->tab[i].Pos;
            r.w = sm->tab[i].Img[0]->w;
            r.h = sm->tab[i].Img[0]->h;
            if(event.button.x >= r.x && event.button.x <= r.x + r.w
            && event.button.y >= r.y && event.button.y <= r.y + r.h) {
                Mix_PlayChannel(-1, sm->click_sound, 0);
                if(sm->tab[i].num == 1) return -1;
                else return 3;
            }
        }
    }
    return 0;
}

void liberer_SousMenu(Menu sm) {
    SDL_FreeSurface(sm.image_bg);
    for(int i = 0; i < sm.nbr_boutton; i++) {
        SDL_FreeSurface(sm.tab[i].Img[0]);
        SDL_FreeSurface(sm.tab[i].Img[1]);
    }
    if(sm.musique) Mix_FreeMusic(sm.musique);
    if(sm.hover_sound) Mix_FreeChunk(sm.hover_sound);
    if(sm.click_sound) Mix_FreeChunk(sm.click_sound);
}

// Sauvegarde et chargement simple
void sauvegarde_jeu(personne p, map m, int score, int vies, int temps) {
    FILE* f = fopen("savegame.txt", "w");
    if(!f) return;
    fprintf(f, "%hd %hd %hd %hd %d %d %d\n",
            p.pos_perso.x, p.pos_perso.y,
            m.pos_map.x, m.pos_map.y,
            score, vies, temps);
    fclose(f);
}

int load_game(personne* p, map* m, int* score, int* vies, int* temps) {
    FILE* f = fopen("savegame.txt", "r");
    if(!f) return -1;
    fscanf(f, "%hd %hd %hd %hd %d %d %d",
           &p->pos_perso.x, &p->pos_perso.y,
           &m->pos_map.x, &m->pos_map.y,
           score, vies, temps);
    fclose(f);
    return 0;
}
