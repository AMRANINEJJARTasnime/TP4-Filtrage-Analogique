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
<img width="404" alt="2" src="https://user-images.githubusercontent.com/121026580/215294175-f1f0c895-918b-41db-a9bd-149bc2af5602.png">
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

4. Choisissez wc qui vous semble optimal. Le filtre est-il bien choisi ? Pourquoi ?

``` matlab
K = 1 ;
new_f=500;
new_wc=2*pi*new_f;
w = 2*pi*f ; 
H = (K*1j*w/new_wc)./(1+1j*w/new_wc) ;
subplot(2,1,1)
plot(f,abs(H),'r')
xlabel('Fréquence (rad/s)');
ylabel('Module de H(f)');
subplot(2,1,2)
yt = y.*H ;
YT = ifft(yt,"symmetric");
YT_temp = fft(YT);
plot(fshift,2*fftshift(abs(YT_temp))/N,'b');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=500');
```

<img width="410" alt="new fc" src="https://user-images.githubusercontent.com/121026580/215293133-2f57a485-99d3-4f7f-aa78-c608144cac55.png">

>Pour ce filtre passe haut on a réussi à atténuer le bruit; en revanche on a perdu beaucoup d informations. Le filtre n'est pas bien choisi.


5. Observez le signal y(t) obtenu, puis Comparer-le avec le signal que vous auriez 
souhaité obtenir.
``` matlab
 subplot(2,2,1)
 plot(t,x)
 title('le signal x(t):');
 subplot(2,2,2)
 plot(t,YT);
 title('le signal x(t) filtré:');
 subplot(2,2,3)
 plot(fshift, fftshift(abs(y)/N)*2);
 title('le spectre x(f):');
 subplot(2,2,4)
plot(fshift,2*fftshift(abs(YT_temp))/N,'b');
xlabel('Fréquence (rad/s)');
title('spectre filtré pour fc=500');

```
<img width="418" alt="comp" src="https://user-images.githubusercontent.com/121026580/215294036-49c3b5b5-8748-4993-b922-28c96de729b5.png">

>Il est impossible pour un filtre passe-haut d'éliminer totalement les données indésirables car il ne peut sélectionner que les fréquences élevées, il y aura toujours des composantes de fréquences basses qui passeront à travers le filtre.

# Dé-bruitage d'un signal sonore

Dans son petit studio du CROUS, un mauvais futur ingénieur a enregistré une 
musique en « .wav » avec un très vieux micro. Le résultat est peu concluant, un bruit 
strident s'est ajouté à sa musique. Heureusement son voisin, expert en traitement du 
signal est là pour le secourir :
« C'est un bruit très haute fréquence, il suffit de le supprimer. » dit-il sûr de lui.

1. Proposer une méthode pour supprimer ce bruit sur le signal.

``` matlab
%%partie2
%qst 1
clear all
close all
clc

[audio, fs] = audioread('test.wav');
sound(audio,fs);
audio = audio';
N=length(audio);
te = 1/fs;
t = (0:N-1)*te;
N = length(audio);

f = (0:N-1)*(fs/N);
fshift = (-N/2:N/2-1)*(fs/N);

spectre_music = fft(audio);
subplot(2,1,1)
plot(t, audio)
 title('le signal de musique :');
subplot(2,1,2)
plot(fshift,fftshift(abs(y)));
title('le spectre de musique:');


%%

k = 1;
fc = 4500;
%la transmitance complexe 
h = k./(1+1j*(f/fc));

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];
y_filtr = spectre_music(1:end-1).*h_filter; % on a diminué du vecteur musique un echantillon
sig_filtred= ifft(y_filtr,"symmetric"); % on restitue le signal filtré
semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)
title('le gain :');
%%
plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))));
sound(sig_filtred,fs);
title('le spectre du signal filtré:');

```
<img width="422" alt="p21 b" src="https://user-images.githubusercontent.com/121026580/215294428-5a914c04-354b-4dc6-9966-571ae07be896.png">
<img width="413" alt="p21 a" src="https://user-images.githubusercontent.com/121026580/215294430-647f25e3-9b7d-4adc-9ab6-189073ecd886.png">
<img width="413" alt="p21" src="https://user-images.githubusercontent.com/121026580/215294431-4bdfcbd2-c1fc-4fd5-b65f-a30f9f0a2207.png">

2. Mettez-la en oeuvre. Quelle influence à le paramètre K du filtre que vous avez 
utilisé ?

``` matlab 

% qst 2

k = 10; %on change la valeur de k de 1 a 10
fc = 4500;
h = k./(1+1j*(f/fc));%la transmitance complexe 
h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];
y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");
semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)
title('le gain = 10 :');
plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))));
%sound(sig_filtred,fs);
title('le spectre du signal filtré:');



```

<img width="412" alt="bb" src="https://user-images.githubusercontent.com/121026580/215294536-0577a656-e371-480e-8e0e-d8a9dd9516d2.png">
<img width="423" alt="b" src="https://user-images.githubusercontent.com/121026580/215294537-b42d0922-51ef-4871-9d50-69b13622eb0b.png">


>Dans le diagramme de gain, la bande passante du signal tend vers 10 cette fois ci au lieu de 1. Le gain statique k est utilisé pour ajuster l'amplitude des fréquences conservées par le filtre

3. Quelles remarques pouvez-vous faire notamment sur la sonorité du signal final.

>On remarque que  lorsqu on a augmenté le gain statique à 10, l'amplitude des fréquences qui passent à travers le filtre est élevée.

4. Améliorer la qualité de filtrage en augmentant l’ordre du filtre.

```matlab
%qst 4
k = 10; %gain statique
fc = 4500; %frequence de coupure

h = k./(1+1j*(f/fc).^100);%la transmitance complexe 

h_filter = [h(1:floor(N/2)), flip(h(1:floor(N/2)))];
y_filtr = spectre_music(1:end-1).*h_filter;
sig_filtred= ifft(y_filtr,"symmetric");
semilogx(f(1:floor(N/2)),abs( h(1:floor(N/2))),'linewidth',1.5)
%%
plot(fshift(1:end-1),fftshift(abs(fft(sig_filtred))));
%sound(sig_filtred,fs);


```

<img width="407" alt="cc" src="https://user-images.githubusercontent.com/121026580/215295143-c608354a-450b-4d84-8390-45f6cab47f6a.png">

>L ordre est 100, le gain est 10

<img width="425" alt="c" src="https://user-images.githubusercontent.com/121026580/215295146-e6ef38ca-33aa-42af-b207-2cc9193c0ec0.png">

>Le spectre du signal filtré


>Une autre solution pour ameliorer le filtre est d augmenter son ordre.
>En augmentant l'ordre d'un filtre, on ajoute des termes supplémentaires à l'équation qui le définit. Cela peut améliorer la réjection des fréquences indésirables.



