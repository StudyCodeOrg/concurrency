# Reordering

### Behaviour depended on flags passed to Java !!!

  - If no flags were passed then it hung!
  - If -Xint (interpret only) was passed then it ran indefinately...
  - If -Xcomp (compile first) then it worked (i.e. might have reordered)

(Using: javac 1.8.0_73, java version "1.8.0_73")

Even then I'm not confident that the behaviour isn't a symptom of my
ignorance. But...

### _Possible_ Example of Reordering

In the Printer.BuildAndPrint method the expected states are:

	lenX:  0  lenY:  0
	lenX:  0  lenY: 40
	lenX: 40  lenY: 40
	lenX: 40  lenY: 80

However, we do get cases where lenX=0 and lenY=80, which I think
means we're getting reordering.

### Sample output:
	
	[ex2-Reorder] java -Xcomp Reorder
	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	===================================
	----------------------------------------------------------------------

	Wooah! x:0 y:70 (Run count: 11)
	[ex2-Reorder]

Update:
-------
Marking x and y as volatile prevents:
  
  1.	the -Xcomp version from stopping, i.e. it never finds evidence
		of reordering.
  2.	the normal/mixed version hanging.

This suggest (to me) that there might actually be reordering and it's
possible the hanging of the 'normal/mixed' version is related to the
reordering (possibly as it switches from intperpreted to compiled).