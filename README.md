# samtools-reheader-fail
Provides a small BAM file to test a potential bug caused by samtools reheader (samtools 1.5)

## Description
Scenario: we want to remove chromosome M (chrM) from BAM file test.bam and then remove the ```"chr"``` prefix from the rest of the chromosomes (and reads) with the use of ```samtools reheader```. We are using samtools 1.5. The same procedure with samtools 0.1.19 completes without problems.

## Resources:
1. [Biostars post 1](https://www.biostars.org/p/128967/)
2. [Biostars post 2](https://www.biostars.org/p/46104/#46114)

## Steps

1. Get chromosome names with samtools idxstats and remove chrM. Then create the new file for which we want to change the header.

```samtools idxstats test.bam | cut -f 1 | grep -vP 'chrM|\*' | xargs samtools view -b test.bam > file.bad```

2. Create the new header

```samtools view -H file.bad | grep -vP '^@SQ\tSN\:chrM' | sed s/chr// > header.sam```

3. Run samtools reheader

```samtools reheader header.sam file.bad > file.uns```

4. Boom

```samtools sort -o file.bam file.uns```

```samtools sort: truncated file. Aborting```
