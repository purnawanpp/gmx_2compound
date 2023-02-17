# gmx_2compound
This tutorial to simulate two compound in GROMACS

#Preparasi Simulasi Dinamika Molekul 2 LIGAND
# Preparasi senyawa Beta-Cyclodextrin (BCD) menggunakan GAFF2 dan penambahan muatan menggunakan Gastaiger disini BCD dijadikan sebagai reseptor, angka 0 tergantung dari net charge pada Chimeara
$ antechamber -i rec.pdb -fi pdb -o rec.gas.mol2 -fo mol2 -at gaff2 -c gas -rn LIG -nc 0
$ parmchk2 -i rec.gas.mol2 -f mol2 -o rec.gas.frcmod

# Preparasi senyawa Floconazole menggunakan GAFF2 dan penambahan muatan menggunakan Gastaiger, disini floconazole sebagai ligand, angka 0 tergantung dari net charge pada Chimeara
$ antechamber -i lig.pdb -fi pdb -o lig.gas.mol2 -fo mol2 -at gaff2 -c gas -rn LIG -nc 2
$ parmchk2 -i lig.gas.mol2 -f mol2 -o lig.gas.frcmod

# Melakukan solvasi dan pengambungan BCD-Fluconazole dengan perintah 
$ tleap -f leap.in

# Lakukan perubahan format amber ke gromacs menggunakan Parmed di google colab, ingat jangan melakukan hidrogen mass repartitioning

# Jalankan perintah berikut satu persatu
$ gmx editconf -f gromacs.gro -o gromacs2.gro -box 10 10 10
$ gmx grompp -f min.mdp -c gromacs2.gro -p gromacs.top -o em.tpr -maxwarn 1
$ gmx mdrun -v -deffnm em
$ export GMX_MAXCONSTRWARN=-1
$ gmx grompp -f nvt.mdp -c em.gro -r em.gro -p gromacs.top -o nvt.tpr -maxwarn 3
$ gmx mdrun -v -deffnm nvt &
$ gmx grompp -f md.mdp -c npt.gro -t npt.cpt -p gromacs.top -o md.tpr -maxwarn 3
$ gmx mdrun -deffnm md


# file xtc tidak ditemukan, maka digunakan file trr untuk melihat trajektorinya
$ gmx trjconv -s md.tpr -f md.trr -o analisis.xtc -pbc mol -ur compact
$ gmx hbond -s md.tpr -f analisis.xtc -num hbond.xvg

