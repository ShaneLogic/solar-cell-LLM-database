# solar-cell-LLM-database 🌞

*SpiroMAPICiTIO2 Perovskite Solar Cell Simulation Database*

---

## 📁 Core data files

### Physics simulation files
Files | Key parameters | Model equation
---|---|---
`Current_flux_density.xlsx` | Current density (J)<br>flux density (Φ) | `∇·J = q(∂n/∂t - G + R)`
`Electrostatic_potential_Electric_field.xlsx` | Electrostatic potential<br>Electric field | Poisson equation
`Potential_Energy.xlsx` | Conduction/Valence band<br>quasi Fermi-level | Self-consistent field calculation
`Recombination.xlsx` | Radiative (BTB)<br>SRH<br>Surface recombination | `R_total = R_rad + R_SRH`

### Device parameter
`layer_unit_test.csv` include：
- Layer thickness（0.5-200 nm）
- Band gap (1.2-1.6 eV)
- Doping concentration (1e15-1e19 cm⁻³)
- Mobility (μ<sub>n</sub>/μ<sub>p</sub>)

---

## 📄 Documentation
`SemiSimu-output.pptx` Technical documentation includes：
```bash
1. Input parameter definition criteria (Section III)
2. Boundary Condition Setting Specifications (Appendix A)
3. Unit conversion table (Appendix B)
