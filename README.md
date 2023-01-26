# Tp4-Ts

<div id="top"></div>


<!-- PROJECT LOGO -->
![logo](https://user-images.githubusercontent.com/80456274/151718182-54d53cc9-69bb-4710-af0e-f2fda10c0743.jpg)

## Objectifs

- Appliquer un filtre réel pour supprimer les composantes indésirables d’un signal. 
- Améliorer la qualité de filtrage en augmentant l’ordre du filtre.

>Commentaires : Il est à remarquer que ce TP traite en principe des signaux continus. 
Or, l'utilisation de Matlab suppose l'échantillonnage du signal. Il faudra donc être 
vigilant par rapport aux différences de traitement entre le temps continu et le temps 
discret.

>Tracé des figures : toutes les figures devront être tracées avec les axes et les 
légendes des axes appropriés.

>Travail demandé : un script Matlab commenté contenant le travail réalisé et des
commentaires sur ce que vous avez compris et pas compris, ou sur ce qui vous a 
semblé intéressant ou pas, bref tout commentaire pertinent sur le TP.

# Filtrage et diagramme de Bode
Sur le réseau électrique, un utilisateur a branché une prise CPL (Courant Porteur en 
Ligne), les signaux utiles sont de fréquences élevées. Le réseau électrique a 
cependant sa propre fréquence (50 hz). Le boiter de réception doit donc pouvoir 
filtrer les basses fréquences pour s'attaquer ensuite à la démodulation du signal utile.

- Schématiquement :

<img width="307" alt="Capture1" src="https://user-images.githubusercontent.com/121026580/214712436-1fe46404-8351-4ece-bfa7-11495c04a0f3.PNG">

- Mathématiquement:
Un tel filtre fournit un signal de sortie en convoluant le signal d'entrée par la réponse temporelle du filtre :
y(t) = x(t) * h(t)

Nous souhaitons appliquer un filtre passe-haut pour supprimer la composante à 50 
Hz. Soit notre signal d'entrée :

x(t) = sin(2.pi.f1.t) + sin(2.pi.f2.t) + sin(2.pi.f3.t)

Avec f1 = 500 Hz, f2 = 400 Hz et f3 = 50 Hz

1- Définir le signal x(t) sur t = [0 5] avec Te = 0,0001 s.

``` matlab

%qst1
te = 1e-4; % le temps d'échantillonnage (te) 
fe=1/te; % la fréquence d'échantillonnage (fe) en Hz
t = 0:te:5-te; % le vecteur de temps en utilisant le temps d'échantillonnage
%les fréquences des signaux sinusoïdaux (en Hz)
f1=500;
f2=400;
f3=50;
 % le signal
x = sin(2*pi*f1*t)+sin(2*pi*f2*t)+sin(2*pi*f3*t);

```

2- Tracer le signal x(t) et sa transformé de Fourrier. Qu'observez-vous ? (Essayez de tracer avec Te = 0,0005 s. Remarques ?)

``` matlab

%qst2
% Afficher le signal x(t) 
subplot(2,1,1)
plot(t,x,'r');
title("signal x(t)");
%Afficher la TF du signal x(t)
subplot(2,1,2)
% Calculer la longueur du signal
N=length(x);
% Calculer la TF du signal x(t)
y=fft(x)
% Décaler la fréquence pour centrer la TF autour de 0
fshift = (-N/2:N/2-1)*(fe/N);
%2*abs(y)/N est pour normaliser l amplitude. Cette normalisation est utilisée pour obtenir une représentation graphique de la TF qui est plus facile à interpréter
plot(fshift,fftshift(2*abs(y)/N)) 
title("TF du signal x(t)");

```
>On constate une perte de précision dans les mesures de fréquence du signal. On a trouve une valeur approchee de 50 : 49.x, pour pouvoir generer la valeur exacte on augmente le nbr d echantillons (t = 0:te:5-te). C est la fuite spectrale

La fonction H(f) (transmittance complexe) du filtre passe haut de premier ordre est 
donnée par :

H(f) = (K.j.w/wc) / (1 + j. w/wc) 

Avec K le gain du signal, w la pulsation et wc la pulsation de coupure.
On se propose de tracer le diagramme de Bode de ce filtre et de l'appliquer au 
signal.

1- Tracer le module de la fonction H(f) avec K=1 et wc = 50 rad/s


