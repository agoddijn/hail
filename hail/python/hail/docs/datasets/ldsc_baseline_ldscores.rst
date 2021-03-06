.. _ldsc_baseline_ldscores:

ldsc_baseline_ldscores
======================

*  **Versions:** 1.1
*  **Reference genome builds:** GRCh37
*  **Type:** :class:`.MatrixTable`

Schema (1.1, GRCh37)
~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

    ----------------------------------------
    Global fields:
        'metadata': struct {
            name: str,
            reference_genome: str,
            n_rows: int32,
            n_cols: int32,
            n_partitions: int32
        }
    ----------------------------------------
    Column fields:
        'annotation': str
        'M_5_50': int32
        'M': int32
    ----------------------------------------
    Row fields:
        'locus': locus<GRCh37>
        'SNP': str
    ----------------------------------------
    Entry fields:
        'x': float64
    ----------------------------------------
    Column key: ['annotation']
    Row key: ['locus']
    ----------------------------------------

