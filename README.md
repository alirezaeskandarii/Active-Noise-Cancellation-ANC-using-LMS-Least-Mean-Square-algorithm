# Active-Noise-Cancellation-ANC-using-LMS-Least-Mean-Square-algorithm
Active Noise Cancellation (ANC) project using the Least Mean Square (LMS) adaptive algorithm. Demonstrates how noise can be suppressed by continuously updating filter coefficients. Includes simulations, adjustable parameters, and visual results for learning adaptive digital signal processing.
%% Upload the voice file with .wav format exactly in the folder which project is saved, and insert its name in the function "audioread"
clc********//------;
clear;
close all;
%% === Load Clean Signal ===
[clean_audio, Fs] = audioread('Voice matlab.wav');
clean_audio = clean_audio(:,1);  % Mono-
d = clean_audio';                % Desired signal
%% === Simulate Noise and Composite Signal ===
noise = 0.05 * randn(size(d));   % Additive noise
v1 = noise;                      % Noise added to the signal
%%x = d + v1;                      % Primary input: noisy signal d(n)+v1(n) = signal+noise
v2 = noise + 0.005 * randn(size(d));  % Reference mic noise (slightly different, to simulate real-world)
%% === LMS Parameters ===
mu = 0.01;         % Step size -> small: stable & slow , large: fast & unsatbel
L = 64 ;            % Filter length v(n-L) & w(L-1) -> past samples
N = length(d);
w = zeros(1, L);   % Adaptive filter weights // weight vector of adaptive filter
%% === k1 and k2 Coefficients ===
k1 = 1.0;          % Weight for desired signal
k2 = 1.0;          % Weight for noise signal
x = k1*d + k2*v1;   % Use v1 as added noise -> apply K1 & k2
%% === Buffers ===
x_buffer = zeros(1, L);
d_hat = zeros(1, N); % final denoised signal
error = zeros(1, N); % learning signal for LMS
%% === LMS Adaptive Filtering ===
for n = L:N
    v2_vec = v2(n:-1:n-L+1); % Reference noise vector
    y = w * v2_vec'; % Filter output
    error(n) = x(n) - y; % Error signal (desired - estimated noise)
    w = w + mu * error(n) * v2_vec; % Update filter weights
    d_hat(n) = error(n); % Store estimate
end
%% === Plot Results ===
t = (0:N-1)/Fs;
figure;
subplot(3,1,1); plot(t, d); title('Original Signal'); xlabel('Time (s)'); grid on;
subplot(3,1,2); plot(t, x); title('Noisy Signal'); xlabel('Time (s)'); grid on;
subplot(3,1,3); plot(t, d_hat); title('Denoised Output (LMS + k1, k2)'); xlabel('Time (s)'); grid on;
figure;
subplot(2,1,1); plot(t, v1); title('Original Noise'); xlabel('Time (s)'); grid on;
subplot(2,1,2); plot(t, error); title('Error Signal'); xlabel('Time (s)'); grid on;
%% === Playback ===
disp('Playing original signal...'); soundsc(d, Fs); pause(length(d)/Fs + 1);
disp('Playing noisy signal...'); soundsc(x, Fs); pause(length(x)/Fs + 1);
disp('Playing denoised signal (LMS filtered)...'); soundsc(d_hat, Fs);pause(length(d_hat)/Fs + 1);
%disp('Playing denoised signal (Error)...'); soundsc(error, Fs);
%%%%%%%
