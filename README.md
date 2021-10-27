# hse21_hw1
SEED=315

mkdir hw

ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq

ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
SEED=315
seqtk sample -s$SEED oil_R1.fastq 5000000 > sub1.fastq
seqtk sample -s$SEED oil_R2.fastq 5000000 > sub2.fastq
seqtk sample -s$SEED oilMP_S4_L001_R1_001.fastq 1500000 > map1.fastq
seqtk sample -s$SEED oilMP_S4_L001_R2_001.fastq 1500000 > map2.fastq

mkdir fastqc

ls sub* | xargs -tI{} fastqc -o fastqc {}

mkdir multiqc

multiqc -o multiqc fastqc


Fastqc sub*.fastq map*.fastq
cp multiqc/multiqc_report.html hse21_hw1/multiqc.html

platanus_trim sub*.fastq
rm sub*.fastq
platanus_internal_trim map*.fastq
rm map*.fastq
platanus scaffold -c out_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed
cp out_scaffold.fa hse21_hw1/hw/scaffold_with_gaps.fasta
platanus gap_close -c out_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed
cp out_gapClosed.fa hse21_hw1/hw/scaffold.fasta
rm *trimmed


sed -n '1,/^>/p' data/scaffold_with_gaps.fasta | head -n -1 >data/longest_with_gaps.fasta
sed -n '1,/^>/p' data/scaffold.fasta | head -n -1 >data/longest.fasta
