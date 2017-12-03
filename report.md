## Project 1: Speech Synthesis and Perception with Envelope Cue

### Code

This is a vectorized version code for every task in this project.

```matlab
% just set these params
N=6;
LPF=50;
is_with_noise=true;

[s, fs] = audioread('C_01_01.wav');
W=divide(N);
I = norm(s);

% generate SSN
noise = 1-2*rand(length(s),1);
[Pxx,w]=pwelch(s,[],[],512,fs);
b=fir2(3000,w/(fs/2),sqrt(Pxx/max(Pxx)));
[h,wh]=freqz(b,1,128);
SSN= filter(b,1,noise);

if is_with_noise
s=s*(norm(SSN)/norm(s))*(1/sqrt(sqrt(10)));
SNR=20*log10(norm(s)/norm(SSN));
s=s+SSN; % sum signal and SSN
end
% sound(s, fs)

b=zeros(9,N);
a=zeros(9,N);
y=zeros(length(s),N);
for i=1:N
    [b(:,i), a(:,i)]=butter(4,[W(1,i) W(1,i+1)]/(fs/2)); % band pass filters
    y(:,i)=filter(b(:,i),a(:,i),s);
end

e=zeros(length(s),N);
[c, d]=butter(6,LPF/(fs/2));    % low pass filter
for i=1:N
    e(:,i)=filter(c,d,abs(y(:,i))); % envelope
end

t=0:1/fs:(length(s)-1)/fs;
t=t';
s_=zeros(length(s),1);
for i=1:N
    s_=s_+e(:,i).*sin(3.14159*(W(1,i)+W(1,i+1))*t);
end

s_=s_*I/norm(s_);
audiowrite(['N=',num2str(N),'LPF=',num2str(LPF),'with_noise.wav'],s_,fs);

sound(s_, fs);
tau=fs/length(s);
Y_=fftshift(abs(fft(s_)));
freq=-fs/2:tau:fs/2-tau;
subplot(2,1,1),plot(freq,abs(fftshift(fft(s)))),title('signal s N=6');
subplot(2,1,2),plot(freq,Y_),title("signal s' N=6");
```

### Task 3

- Generate a noisy signal (summing clean sentence and SSN) at SNR -5 dB. 
- Set LPF cut-off frequency to 50 Hz. 
- Implement tone-vocoder by changing the number of bands to N=2, N=4, N=6, N=8, and N=16. 
- Describe how the number of bands affects the intelligibility of synthesized sentence,and compare findings with those obtained in task 1. 

#### Conclusion3

When the number of bands N ≤ 8, the speech can not be recognized; when N = 16, the speech can be hardly recognized.
