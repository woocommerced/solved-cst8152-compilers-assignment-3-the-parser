Download Link: https://assignmentchef.com/product/solved-cst8152-compilers-assignment-3-the-parser
<br>
<strong>NOTE:<u> This Assignment cannot be late more than 5 days (see Bonus)</u> </strong>

<strong><u>Purpose:</u> Implementing a Parser and more… </strong>




Assignment #3 consists of two parts. Part 1 is mandatory. It involves the PLATYPUS Parser implementation. Part 2 is an optional bonus for those of you who would like to have some challenge and would want to earn some additional marks (up to 10%). Part 2 includes some rudimentary semantic analysis, writing a PLATYPUS cross-compiler, or writing a PLATYPUS interpreter.




<h1>Part 1. Implementing a PLATYPUS Parser (17 marks)</h1>




You are cordially invited to (in other words, you have to) write a <strong><em>Recursive Descent </em></strong>

<strong><em>Predictive Parser</em></strong> (<strong>RDPP</strong>) for the <strong>PLATYPUS</strong> language. You are to integrate the Parser with your existing lexical analyzer and symbol table in order to complete the front-end of your PLATYPUS compiler. The implementation is broken into two tasks.




<strong><u>Task 1. Modifying the Grammar (5 marks).</u> </strong>




In order to build a RDPP you need to modify the <strong>syntactical part</strong> of the PLATYPUS Grammar (The Platypus Syntactic Specification). The grammar provided for you in <strong><em>PlatypusLGR_F18,doc</em></strong> is an LR grammar (that is, a grammar suitable for LR parsing). You must transform it into an LL grammar suitable for <strong><em>Recursive Descent Predictive Parsing</em></strong>. To accomplish that you should follow the steps outlined bellow.




<ol>

 <li>Check the PLATYPUS Grammar for completeness and correctness.</li>

 <li>Eliminate the <strong><em>left recursion</em></strong> and apply <strong><em>left factoring</em></strong> where needed.</li>

</ol>




Some of the syntactic productions must be rewritten to make them suitable for recursive decent predictive parsing. Do not forget that our grammar (<strong><em>PlatypusLGR_F18.doc</em></strong>) is an LR grammar, which must be transformed into an equivalent LL grammar. For example, the productions of the type




&lt;statements&gt; -&gt; &lt;statement&gt; | &lt;statements&gt; &lt;statement&gt;




can be rearranged in a convenient for transformation form




&lt;statements&gt; -&gt; &lt;statements&gt; &lt;statement&gt; | &lt;statement&gt;

Once you rearrange the production, you must eliminate the immediate left-recursion. The transformation will produce the following two new productions:




&lt;statements&gt; -&gt; &lt;statement&gt; &lt;statements’&gt;

&lt;statements’&gt; -&gt; &lt;statement&gt; &lt;statements’&gt; | 




In some cases it is possible to rework the grammar to avoid some of the transformations. This approach is often applied to production, which contain a “leftfactor.” For example, the output statement




&lt;output statement&gt; -&gt;

OUTPUT (&lt;opt_variable list&gt;);

| OUTPUT (&lt;string literal&gt;);




can be reworked in the following way:




&lt;output statement&gt; -&gt;

OUTPUT (&lt;output list&gt;);




&lt;output list &gt; -&gt; &lt;opt_variable list&gt; | STR_T




where STR_T is a string literal token produced by the scanner.



The &lt;output statement&gt; production does not contain a left-factor anymore.




It is also possible to simplify the grammar applying formal simplification procedures (see pages 223-224 (old 183-185) in your textbook). Do not simplify the grammar for this assignment.




<ol start="3">

 <li>Build the FIRST set for the <strong>syntactic</strong></li>

</ol>




After you transform the grammar you must build the <strong>FIRST set</strong> for each of the grammar productions (nonterminals). A FIRST set for a production (nonterminal) is a set of terminals that appear as left-most symbols in any of the production alternatives.  When you build the FIRST set, if some of the elements of the set are non-terminals, they must be replaced by their FIRST sets and so on. The final FIRST set must contain only <strong>terminals</strong> (tokens). The elements of the set must be <strong>unique</strong>. This is essential for the predictive parser. If they are not unique, the left factoring transformation must be applied to the corresponding production.




<ol start="4">

 <li><strong>Part 1 – Task 1 Submission:</strong> Write the <strong><em>entire syntactic grammar</em></strong> and the corresponding FIRST sets. Do not remove the original productions. If a production is to be transformed, write the <strong>modification</strong> below the original production and <strong>indicate clearly</strong> the type of the changes applied to the original production. Do not include the provided explanatory text (only the productions). Write your name on every page.</li>

</ol>

Now you are ready for the next task. You can work on both tasks simultaneously – production by production and function by function. I strongly recommend this approach.

<h2>Task 2. Writing the Parser(12 marks)</h2>




To build the RDPP follow the steps outlined below.




<strong>Step 1:</strong>




Name your RDPP source code file <strong><em>parser.c</em></strong>. Create <strong><em>parser.h</em></strong>.  Include the required system and user header files. Define one static global variable: <strong><em>lookahead </em></strong> of type <strong><em>Token</em></strong>. Additionally define a global variable <strong>synerrno</strong> of type <strong><em>int</em></strong>. You may add additional variable declarations and constants definitions, if necessary (and it is). Then declare the functions used in the parser implementation. All function prototypes, variable definitions/declarations, and constant definitions must be in <strong><em>parser.h</em></strong>.

<strong> </strong>

<strong>Step 2:</strong>




Write your <strong><em>parser()</em></strong> function.




void parser(void){

lookahead = malar_next_token();   program(); match(SEOF_T,NO_ATTR);

gen_incode(“PLATY: Source file parsed”);

}




<strong>Step 3:</strong>




Write your <strong><em>match()</em></strong>  function. The prototype for the function is:




void match(int pr_token_code,int pr_token_attribute);




The <strong><em>match()</em></strong>  function matches two tokens: the current input token (<strong><em>lookahead</em></strong>) and the token required by the parser. The token required by the parser is represented by two integers – the token code (<strong><em>pr_token_code</em></strong>), and the token attribute

(<strong><em>pr_token_attribute</em></strong>). The attribute code is used only when the token code is one of the following codes: <strong>KW_T</strong>, <strong>LOG_OP_T</strong>, <strong>ART_OP_T</strong>, <strong>REL_OP_T</strong>. In all other cases the token code is matched only.

If the match is successful and the <strong><em>lookahead</em></strong> is <strong>SEOF_T</strong>, the function returns. If the match is successful and the <strong><em>lookahead</em></strong> is not  <strong>SEOF_T</strong>, the function advances to the next input token by executing the statement:




lookahead = malar_next_token ();




If the new lookahead token is <strong>ERR_T</strong>, the function calls the error printing function <strong><em>syn_printe()</em></strong>, advances to the next input token by calling<em> <strong>malar_next_token ()</strong></em> again,  increments the error counter <strong><em>synerrno</em></strong>, and returns.

If the match is unsuccessful, the function calls the error handler <strong><em>syn_eh(pr_token_code)</em></strong> and returns.




<strong>Note:</strong> Make your <strong><em>match()</em></strong> function as efficient as possible. This function is called many times during the parsing. The function will be graded with respect to design and efficiency.




<strong>Step 4:</strong>




Write the error handling function <strong><em>syn_eh()</em></strong>. This function implements a simple panic mode error recovery.




void syn_eh(int sync_token_code)




First, the function calls <strong><em>syn_printe()</em> </strong>and increments the error counter. Then the function implements a panic mode error recovery: the function advances the input token (<strong><em>lookahead</em></strong>) until it finds a token code matching the one required by the parser (<strong><em>pr_token_code </em></strong>passed to the function as <strong><em>sync_token_code </em></strong>).




It is possible, when advancing, that the function can reach the end of the source file without finding the matching token. To prevent from overrunning the input buffer, before every move the function checks if the end of the file is reached. If the function looks for <strong><em>sync_token_code</em></strong> different from <strong>SEOF_T</strong> and reaches the end of the source file, the function calls <strong><em>exit(synerrno)</em></strong>.

If a matching token is found and the matching token is not <strong>SEOF_T</strong>, the function advances the input token one more time and returns. If a matching token is found and the matching token is <strong>SEOF_T,</strong> the function returns.




<strong>Step 5:</strong>

Write the error printing function <strong><em>syn_printe()</em></strong> .




void syn_printe()




Note: This function implementation is provided for you in Assignment3MPTF_F18.zip.




The function prints the following error message:




PLATY: Syntax error: Line: <em>line_number_of_the_syntax_error </em>

<em>***** </em> Token code:<em>lookahead token code </em>Attribute<em>: token attribute </em> and returns. For example:




PLATY: Syntax error:  Line: 2

*****  Token code: 13 Attribute: NA

PLATY: Syntax error:  Line: 8

*****  Token code:  9 Attribute: 0

PLATY: Syntax error:  Line: 9

*****  Token code:  2 Attribute: sum

PLATY: Syntax error:  Line: 11

*****  Token code:  4 Attribute: 0.5

PLATY: Syntax error:  Line: 17

*****  Token code:  6 Attribute: Result:

PLATY: Syntax error:  Line: 21

*****  Token code: 16 Attribute: ELSE




If the offending token is a keyword, variable identifier or string literal you <strong>must</strong> use the corresponding token attribute to access and print the lexeme (keyword name, variable name, or string).

For example, to print the keyword lexeme you must use the <strong><em>kw_table</em></strong> defined in <strong><em>table.h</em></strong>. <strong>Important note:</strong> You are not allowed to copy the keyword table in <em>parser.h</em> or <em>parser.c</em>. You must use a proper declaration to create an external link to the one defined in <em>table.h</em>.

Similarly, you must use the string literal table to print the sting literals.




<strong>Step 6: </strong>




Write the<strong> gen_incode<em>()</em> </strong>function. In Part 1 of this assignment the function takes a string as an argument and prints it. Later the function can be modified and used to emit intermediate (Bonus 1) or machine code. The function may be called any time a production is recognized (see <strong><em>parser()</em></strong>). The format of the message is: “PLATY: Program parsed”, “PLATY: Assignment statement parsed”, and so on (see the sample output files).




<strong>Step 7:</strong>




For each of your grammar productions write a function named after the name of the production. For example:




void program(void){

match(KW_T,PLATYPUS);match(LBR_T,NO_ATTR);opt_statements();

match(RBR_T,NO_ATTR);

gen_incode(“PLATY: Program parsed”);

}




Writing a production function, follow the substeps below.




<strong>Step 7.1:</strong>




To implement the Parser, you <strong>must use</strong> the modified grammar (see Task 1). Before writing a function, analyze carefully the production. If the production consists of a single production rule (no alternatives), write the corresponding function without using the FIRST set (see above). If you use the <strong><em>lookahead </em></strong>to verify in advance whether to proceed with the production and call the <strong><em>syn_printe()</em></strong> function, your output might report quite different syntax errors than my parser will reports.

<strong>Example:</strong>The production:




&lt;input statement&gt; -&gt;

INPUT (&lt;variable list&gt;);

<strong> </strong>

<strong>MUST</strong> be implemented as follows:

void input_statement(void){

match(KW_T,INPUT);match(LPR_T,NO_ATTR);variable_list();    match(RPR_T,NO_ATTR); match(EOS_T,NO_ATTR);     gen_incode(“PLATY: Input statement parsed”);

}

<strong>AND MUST NOT be implemented as shown below</strong>:

void input_statement(void){   if(lookahead.code == KW_T

&amp;&amp; lookahead.attribute.get_int== INPUT) {    match(KW_T,INPUT);match(LPR_T,NO_ATTR);variable_list();    match(RPR_T,NO_ATTR); match(EOS_T,NO_ATTR);     gen_incode(“PLATY: Input statement parsed”);

}else    syn_printe();

}

This implementation will “catch” the syntax error but will prevent the <strong><em>match()</em></strong> function from calling the error handler at the right place.




<strong>Step 7.2:</strong>

If a production has more than one alternatives on the right side (even if one of them is empty), you must use the FIRST set for the production.

For example, the FIRST set for the &lt;opt_statements&gt; production is: { <strong>KW_T</strong> (but not

<strong>PLATYPUS, ELSE, THEN, REPEAT, TRUE, FALSE</strong>), <strong>AVID_T, SVID_T</strong>, and . Here is an example how the FIRST set is used to write a function for a production: void opt_statements(){

/* FIRST set: {AVID_T,SVID_T,KW_T(but not … see above),e} */ switch(lookahead.code){    case AVID_T:

case SVID_T: statements();break;    case KW_T:

/* check for PLATYPUS, ELSE, THEN, REPEAT, TRUE, FALSE here      and in statements_p()*/    if (lookahead. attribute.get_int != PLATYPUS          &amp;&amp; lookahead.attribute.get_int != ELSE

&amp;&amp; lookahead.attribute.get_int != THEN

&amp;&amp; lookahead.attribute.get_int != REPEAT

&amp;&amp; lookahead.attribute.get_int != TRUE          &amp;&amp; lookahead.attribute.get_int != FALSE){      statements();  break;     }

default: /*empty string – optional statements*/ ;       gen_incode(“PLATY: Opt_statements parsed”);

}

}

Pay special attention to the implementation of the empty string. If you do not have an empty string in your production, you must call the <strong><em>syn_printe() </em></strong>function at that point.




<strong>IMPORTANT NOTE:</strong> You are not allowed to call the error handling function <strong><em>syn_eh()</em></strong> inside a production function and you are not allowed to advance the <strong><em>lookahead</em></strong> within a production function as well. Only <strong><em>match()</em></strong> can call <strong><em>syn_eh()</em></strong>, and only <strong><em>match()</em></strong> and <strong><em>syn_eh()</em></strong> can advance <strong><em>lookahead.</em></strong>




<strong>ANOTHER NOTE:</strong> Each function must contain a line in its header indicating the production it implements and the FIRST set for that production (see above).




<strong>Step 8: </strong>




Build your parser incrementally, function by function. After adding a function, test the parser thoroughly. Use your main program (modify one of the previous main programs) to test the parser.







<h1>Part 2. Optional Bonus (up to 10 marks)</h1>

<strong> </strong>

<strong><u>Bonus Due Date:</u></strong> prior to or on <strong>December 14<sup>th</sup>, 2018 </strong>

<h2>Bonus 1 – Semantic Analysis in the Parser (2 marks)</h2>

The PLATYPUS language specification (<strong><em>PlatypusILS_F18.pdf</em></strong>) stipulates that by default, the type of a variable is defined by the first and the last symbol of the variable identifier. To implement this bonus you must modify the <strong><em>scanner.c</em></strong> and determine the type of the variable during the scanning process. Use the <em>flags</em> variable in the <em>AdditionalVidTokenAttributes</em> structure to store the new variable type. <strong><em> </em></strong>




<h2>Bonus 2 – PLATYPUS Cross-compiler (7 marks)</h2>




Write a PLATYPUS cross-compiler. Your compiler (parser) must generate a complete ANSI C program, which when compiled must produce exactly the same results as the originating PLATYPUS program given the same input data. This task is relatively easy. Use the <strong><em>gen_incode()</em></strong> function to generate C-code to a file. This implementation might require some modification of the <strong><em>main()</em></strong>, <strong><em>gen_incode</em></strong>, and the <strong><em>match()</em></strong> functions. I will provide them you with some information about how to build the cross-compiler.

<h2>Bonus 3 – PLATYPUS Interpreter (10 marks)</h2>

<strong><em> </em></strong>

Write a PLATYPUS Interpreter.  This task is more difficult than the task above. You need to add some new data structures (stacks and lists), but again with some modifications to the parser you can turn it into an interpreter. Those of you who decide to implement this option should talk to me. I will provide them you with some information about how to build the interpreter.







<strong>NOTE:  In order to earn the optional bonus marks you must:  </strong>

<ul>

 <li>Have a <strong>working</strong> PLATYPUS compiler project as specified by Assignment 3<strong>.</strong></li>

 <li>Submit your bonus program <strong><em>separately</em></strong> from the required assignment submission in a folder named<strong> bonus1, bonus2, </strong>or<strong> bonus3</strong>.</li>

 <li>Explain briefly in writing which option you implemented and how you implemented it.</li>

 <li>Document thoroughly your implementation code.</li>

 <li>Provide your own test file(s) and an explanation how to use them to prove that your implementation “works.”</li>

</ul>




<strong>What to Submit for Part 1 (Task 1 and Task 2):</strong>




Both paper and digital submission are required for this assignment.




The submission MUST follow the Assignment Submission Standard. No test plan is required for this assignment. The function headers of the parser functions should contain only the following: <strong>the grammar production the function implements (e.g. &lt;program&gt;  -&gt;  PLATYPUS { &lt;opt_statements&gt; }), the FIRST set for the production(e.g. FIRST(&lt;program&gt;= {KW_T (PLATYPUS)}), and the author name (only if you work in a team). </strong>

<strong> </strong>

<h2>Paper Submission</h2>

Print the modified syntactic part of the PLATYPUS grammar and the FIRST sets.<strong> Indicate</strong> clearly all the changes to the grammar.<strong> Indicate</strong> what kind of transformations you have used to modify the grammar. Remember that the transformed grammar must be <strong><em>equivalent</em></strong> to the original one. Do not make your PLATYPUS a <strong>S</strong>PLATYPUS (<strong>S</strong> for Sad). Every page must have the name of the author (author for teams) on the top.

<strong> </strong>

<h2>Digital Submission</h2>

<strong><u>Compress</u></strong> into a <strong>zip (not rar)</strong> file the following files: the document(s) you have printed for the paper submission (in MS Word or pdf format); all project’s <strong>.h</strong> files and <strong>.c</strong> files; all <strong>.pls</strong> files and the output test files produced by your program. Include your additional input/output test files if you have any. Your bonus (if any) must be in a separate folder.

Upload the zip file on Brightspace using the Assignment 3 link in the Assignments folder on Brightspace. The file must be submitted prior or on the due date as indicated in the assignment. The name of the file must be Your Last Name followed by the last three digits of your student number followed by cA3. For example: Ranev345_cA3.zip. If your last name is long, you can truncate it to the first 5 letters. <strong>Teams</strong> must submit one .zip file only. The name of the file must contain the names of both members e.g. Ranev345_Me123_cA3.zip.




This assignment <strong>cannot be late more than 5 days – </strong>see the Marking Sheet for this assignment. If you submit a late bonus you must submit it by email as an attachment. You should receive an acknowledgement by me.




<strong>Important Note: </strong>If you have already worked on the Scanner in a team you can keep working in the same team but you cannot create new teams. If you decide to work on some of bonuses as a team, the bonus mark will be divided by 2.

<strong> </strong>

<strong>THE LAST IMPORTANT NOTE: </strong>All assignments must be completed to receive credit for the course (see Course Outline).




Enjoy the assignment and do not forget that:




“<em>It is better to know some of the question than all of the answers</em>.” James Thruber




And remember to remember:




<em>“If you think that education is expensive, try ignorance”</em> Derek Bok




and also




(Please see next page)

Write and sing:




Write in C (“Let it Be”)

————————

When I find my code in tons of trouble, Friends and colleagues come to me, Speaking words of wisdom:

“Write in C.”




As the deadline fast approaches, And bugs are all that I can see,

Somewhere, someone whispers:

“Write in C.”




Write in C, Write in C, Write in C, oh, Write in C. COBOL’s old and blurred, Write in C.




I used to write a lot of FORTRAN, For science it worked flawlessly.

Try using it for graphics!

Write in C.




When I spend an endless night, Debugging some assembly, Something, somewhere tells me:

“Write in C.”




Write in C, Write in C,

Write in C, yeah, Write in C.

BASIC’s not the answer.

Write in C.




These days Java came to town.

It perfectly compiles to code named B, But it’s sometimes slow when turns around. Write in C.




Write in C, Write in C

Write in C, oh, Write in C.

Pascal won’t quite cut it.

Write in C.




A fake C sharps around trying to bring Java down.

It’s sharp but can’t cut a stone, so please live C alone.




Write in C, Write in C Write in C, oh, Write in C.

C is sharp and Sharp isn’t it. Write in C.




J <em>Your turn here, add a verse or two</em> J

(see Write_in_C_Poems.zip)