% M-file: torque_speed_curve.m
% Plot torque-speed curves of an induction motor with different rotor resistances.

% Motor parameters
r1 = 0.641;           % Stator resistance (ohms)
x1 = 1.106;           % Stator reactance (ohms)
r2 = 0.332;           % Rotor resistance (ohms)
x2 = 0.464;           % Rotor reactance (ohms)
xm = 26.3;            % Magnetization reactance (ohms)
v_phase = 460 / sqrt(3);  % Phase voltage (V)
n_sync = 1800;            % Synchronous speed (rpm)
w_sync = 2 * pi * n_sync / 60;  % Synchronous speed (rad/s)

% Thevenin equivalent voltage and impedance
z_th = 1i * xm * (r1 + 1i * x1) / (r1 + 1i * (x1 + xm));
v_th = v_phase * (1i * xm) / (r1 + 1i * (x1 + xm));
r_th = real(z_th);
x_th = imag(z_th);

% Slip range and mechanical speed
s = linspace(0.001, 1, 51);
n = (1 - s) * n_sync;

% Initialize torque arrays
t_ind1 = zeros(size(s));  % Original R2
t_ind2 = zeros(size(s));  % Doubled R2
t_ind3 = zeros(size(s));  % Halved R2

% Calculate torque for original, doubled, and halved R2
for ii = 1:length(s)
    t_ind1(ii) = (3 * abs(v_th)^2 * r2 / s(ii)) / ...
        (w_sync * ((r_th + r2 / s(ii))^2 + (x_th + x2)^2));
    
    t_ind2(ii) = (3 * abs(v_th)^2 * (2 * r2) / s(ii)) / ...
        (w_sync * ((r_th + (2 * r2) / s(ii))^2 + (x_th + x2)^2));
    
    t_ind3(ii) = (3 * abs(v_th)^2 * (0.5 * r2) / s(ii)) / ...
        (w_sync * ((r_th + (0.5 * r2) / s(ii))^2 + (x_th + x2)^2));
end

% Plot the torque-speed curves
figure;
plot(n, t_ind1, '-', n, t_ind2, '--', n, t_ind3, ':', 'LineWidth', 2);
xlabel('Speed (rpm)');
ylabel('Torque (Nm)');
legend('R2', '2R2', '0.5R2', 'Location', 'southwest');
grid on;