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

``` matlab
%qst1 partie2 
te=1e-4;
fe=1/te;
t=0:te:5-te;
N=length(t);
k=1;
f=(0:N-1)*(fe/N);
wc=50; % pulsation de coupure
w=2*pi*f; % frequence (rad)

H = ((k*j*w)/wc)./(1+(j*w)/wc);
module= abs(H);
plot(w,module,'r');
xlabel('Fréquence (rad/s)');
ylabel('Module de H(f)');
```
<img width="399" alt="1 2 1" src="https://user-images.githubusercontent.com/121026580/215257384-a09b166c-0a07-4ff9-88d7-150f77b9c047.png">

2. Tracer 20.log(|H(f)|) pour différentes pulsations de coupure wc, qu'observez-vous? (Afficher avec semilogx)
 ``` matlab
 
 
te=1e-4;
fe=1/te;
t=0:te:5-te;
N=length(t);
f=(0:N-1)*(fe/N);
K = 1 ;
f1=50;
f2=500;
f3=1000;

wc1=2*pi*f1;
wc2=2*pi*f2;
wc3=2*pi*f3;


w = 2*pi*f ; 

H1 = (K*1j*w/wc1)./(1+1j*w/wc1) ;
H2 = (K*1j*w/wc2)./(1+1j*w/wc2) ;
H3 = (K*1j*w/wc3)./(1+1j*w/wc3) ;
 
module1= abs(H1);
signal1= 20*log(module1);
module2= abs(H2);
signal2= 20*log(module2);
module3= abs(H3);
signal3= 20*log(module3);
 
semilogx(f,signal1,'g',f,signal2,'r',f,signal3,'b')
ylabel('Gain (dB)')
xlabel('Frequence (rad/s)')
title('Bode Diagram')
legend('fc = 50 rad/s', 'fc = 500 rad/s', 'fc = 1000 rad/s');
grid on 
 

 ```

<img width="407" alt="1 2 2" src="https://user-images.githubusercontent.com/121026580/215260165-730f7295-ddc5-476e-921a-ac05c7f95227.png">

>On constate que la pente du gain augmente lorsque la fréquence de coupure augmente. Cela signifie que pour une fréquence de coupure plus basse, on atteind la bande passante plus rapidement, c a d que pour les fc plus hautes on a une perte d'informations.


3. Choisissez différentes fréquences de coupure et appliquez ce filtrage dans 
l'espace des fréquences. 
``` matlab 
%qst3
te=1e-4;
fe=1/te;
t=0:te:5-te;
N=length(t);
k=1;
f=(0:N-1)*(fe/N);
fshift = (-N/2:N/2-1)*(fe/N);

f1=50;
f2=500;
f3=1000;
wc1=2*pi*f1;
wc2=2*pi*f2;
wc3=2*pi*f3;


w=2*pi*f; % frequence (rad)
H1 = ((k*j*w)/wc1)./(1+(j*w)/wc1);
H2 = ((k*j*w)/wc2)./(1+(j*w)/wc2);
H3 = ((k*j*w)/wc3)./(1+(j*w)/wc3);

x = sin(2*pi*500*t)+ sin(2*pi*400*t)+ sin(2*pi* 50*t) ;
y=fft(x);
yt1 = y.*H1 ;
yt2 = y.*H2 ;
yt3 = y.*H3 ;
YT1 = ifft(yt1,"symmetric");
YT2 = ifft(yt2,"symmetric");
YT3 = ifft(yt3,"symmetric");
YT1_temp = fft(YT1);
YT2_temp = fft(YT2);
YT3_temp = fft(YT3);
subplot(2,2,1) 
plot(fshift,2*fftshift(abs(YT1_temp))/N,'b');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=50rad/s');
subplot(2,2,2)
plot(fshift,2*fftshift(abs(YT2_temp))/N,'g');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=500rad/s');
subplot(2,2,3)
plot(fshift,2*fftshift(abs(YT3_temp))/N,'r');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=1000rad/s');
```

<img width="392" alt="123333" src="https://user-images.githubusercontent.com/121026580/215262983-46c03040-016f-41e0-b427-877ab1be364c.png">

4. Choisissez wc qui vous semble optimal. Le filtre est-il bien choisi ? Pourquoi ?

>D'apres la question precedente on constate que fc=50 est la frequence optimale. on a obtenu un filtre dont on a pas perdu beaucoup d informations.


5. Observez le signal y(t) obtenu, puis Comparer-le avec le signal que vous auriez 
souhaité obtenir.

``` matlab
%qst3
te=1e-4;
fe=1/te;
t=0:te:5-te;
N=length(t);
k=1;
f=(0:N-1)*(fe/N);
fshift = (-N/2:N/2-1)*(fe/N);
f1=50;
f2=500;
f3=1000;
wc1=2*pi*f1;
wc2=2*pi*f2;
wc3=2*pi*f3;

w=2*pi*f; % frequence (rad)
H1 = ((k*j*w)/wc1)./(1+(j*w)/wc1);
H2 = ((k*j*w)/wc2)./(1+(j*w)/wc2);
H3 = ((k*j*w)/wc3)./(1+(j*w)/wc3);

x = sin(2*pi*500*t)+ sin(2*pi*400*t)+ sin(2*pi* 50*t) ;
y=fft(x);
yt1 = y.*H1 ;
yt2 = y.*H2 ;
yt3 = y.*H3 ;
YT1 = ifft(yt1,"symmetric");
YT2 = ifft(yt2,"symmetric");
YT3 = ifft(yt3,"symmetric");
YT1_temp = fft(YT1);
YT2_temp = fft(YT2);
YT3_temp = fft(YT3);
subplot(2,2,1) 
plot(fshift,2*fftshift(abs(y))/N);
xlabel('Fréquence (rad/s)');
title('spectre d amplitude');
subplot(2,2,2)
plot(fshift,2*fftshift(abs(YT1_temp))/N,'b');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=50rad/s');
subplot(2,2,3)
plot(fshift,2*fftshift(abs(YT2_temp))/N,'g');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=500rad/s');
subplot(2,2,4)
plot(fshift,2*fftshift(abs(YT3_temp))/N,'r');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=1000rad/s');

```

<img width="402" alt="123" src="https://user-images.githubusercontent.com/121026580/215262782-e5200d46-7602-4170-8ea3-0294e487aac6.png">

# Dé-bruitage d'un signal sonore

Dans son petit studio du CROUS, un mauvais futur ingénieur a enregistré une 
musique en « .wav » avec un très vieux micro. Le résultat est peu concluant, un bruit 
strident s'est ajouté à sa musique. Heureusement son voisin, expert en traitement du 
signal est là pour le secourir :
« C'est un bruit très haute fréquence, il suffit de le supprimer. » dit-il sûr de lui.

1. Proposer une méthode pour supprimer ce bruit sur le signal.

``` matlab
[music, fs] = audioread('test.wav');

music = music';
N=length(music);
te = 1/fs;
t = (0:N-1)*te;

f = (0:N-1)*(fs/N);
fshift = (-N/2:N/2-1)*(fs/N);

y_trans = fft(music);
subplot(3,1,1)
plot(t,y)
subplot(3,1,2)
plot(fshift,fftshift(abs(y_trans)))

```


