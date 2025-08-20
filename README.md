CASO A
public class ViajeCamion {
    public static void main(String[] args) {
        // === 1) Datos base (Caso A) ===
        double d1 = 42.0, d2 = 58.5, d3 = 37.2;
        double t1 = 0.9, t2 = 1.4, t3 = 0.8;
        double l1 = 5.1, l2 = 6.9, l3 = 4.3;
        double precioLitro = 1.35;
        double masaCargaKg = 1200;
        double largo = 2.0, ancho = 1.2, alto = 1.1;
        double p1 = 2.5, p2 = 3.0, p3 = 2.0;
        double deprecPorKm = 0.08;
        double volCamionM3 = 10.0;
        double fCO2 = 2.68;
        double galPorLitro = 0.264172;
        double vmin = 30, vmax = 90;
        double va = 40, vb = 80, ca = 0.05, cb = 0.09;
        double a = -0.0008, b = 0.08, c = 4.0;

        // === 2) Velocidades y rendimiento ===
        double v1 = d1 / t1;
        double v2 = d2 / t2;
        double v3 = d3 / t3;
        double vprom = (d1 * v1 + d2 * v2 + d3 * v3) / (d1 + d2 + d3);

        double kml1 = d1 / l1;
        double kml2 = d2 / l2;
        double kml3 = d3 / l3;
        double kmltotal = (d1 + d2 + d3) / (l1 + l2 + l3);

        // === 3) Costos directos ===
        double costoComb = (l1 + l2 + l3) * precioLitro;
        double deprec = (d1 + d2 + d3) * deprecPorKm;
        double peajes = p1 + p2 + p3;
        double costoDirecto = costoComb + deprec + peajes;
        double costoPorKm = costoDirecto / (d1 + d2 + d3);

        // === 4) Volumen, densidad y ocupación ===
        double volumenM3 = largo * ancho * alto;
        double densidad = masaCargaKg / volumenM3;
        double ocupacion = volumenM3 / volCamionM3;

        // === 5) Emisiones de CO2 ===
        double COTotal2 = (l1 + l2 + l3) * fCO2;
        double COporKm2 = COTotal2 / (d1 + d2 + d3);

        // === 6) Conversiones y normalización ===
        double vprom_ms = vprom * 1000 / 3600;
        double galTot = (l1 + l2 + l3) * galPorLitro;
        double vnorm = (vprom - vmin) / (vmax - vmin);

        // === 7) Promedios y dispersión ===
        double vmedia = (v1 + v2 + v3) / 3;
        double sigma = Math.sqrt(
            ((v1 - vmedia) * (v1 - vmedia) + (v2 - vmedia) * (v2 - vmedia) + (v3 - vmedia) * (v3 - vmedia)) / 3
        );
        double vpond_t = (t1 * v1 + t2 * v2 + t3 * v3) / (t1 + t2 + t3);

        // === 8) Interpolación lineal (mantenimiento) ===
        double c_vprom = ca + (cb - ca) * (vprom - va) / (vb - va);
        double costoMant = c_vprom * (d1 + d2 + d3);

        // === 9) Modelo polinómico de rendimiento ===
        double kmlModelo = a * vprom * vprom + b * vprom + c;
        double litrosModelo = (d1 + d2 + d3) / kmlModelo;

        // === 10) Índice de eficiencia (score) ===
        // Usando los pesos w1 = 0.25, w2 = 0.25, w3 = 0.25, w4 = 0.25 (puedes cambiarlos si el taller especifica otros)
        double w1 = 0.25, w2 = 0.25, w3 = 0.25, w4 = 0.25;
        double score = w1 * (1 / costoPorKm) + w2 * kmltotal + w3 * (1 / COporKm2) + w4 * (1 / (1 + sigma));

        // === Mostrar resultados ===
        System.out.println("\n==== Resultados ====");
        System.out.printf("v1 = %.2f, v2 = %.2f, v3 = %.2f km/h\n", v1, v2, v3);
        System.out.printf("vprom = %.2f km/h = %.3f m/s\n", vprom, vprom_ms);
        System.out.printf("kml1 = %.2f, kml2 = %.2f, kml3 = %.2f, kmltotal = %.3f\n", kml1, kml2, kml3, kmltotal);

        System.out.printf("Comb = %.3f, Dep = %.3f, Peajes = %.3f, Directo = %.3f, /km = %.3f\n",
                costoComb, deprec, peajes, costoDirecto, costoPorKm);

        System.out.printf("Volumen = %.3f m3, Densidad = %.2f kg/m3, Ocupación = %.3f\n",
                volumenM3, densidad, ocupacion);

        System.out.printf("COTotal2 = %.3f kg, COporKm2 = %.3f kg/km, galTot = %.4f\n",
                COTotal2, COporKm2, galTot);

        System.out.printf("vmedia = %.3f, vpond_t = %.3f, sigma = %.3f, vnorm = %.3f\n",
                vmedia, vpond_t, sigma, vnorm);

        System.out.printf("c(vprom) = %.5f, costoMant = %.3f, kmlModelo = %.3f, LitrosModelo = %.3f\n",
                c_vprom, costoMant, kmlModelo, litrosModelo);

        System.out.printf("Score = %.4f\n", score);
    }
}
