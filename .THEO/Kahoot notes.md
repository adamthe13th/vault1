- the problem: in a boolean formula, is there a Belegung that sets at most 7 variables to 1 
	- this is in P because Kombinatorish n/ k is O(n^k )
- the problem: same thing but where half the variable is set to one is exponentiell because it because C_n )^ 2n
	- any time some combination problem include an n-dependent variable in place of k-> Anzahl wächst exponentiel
- P abzählbar because P in Menge der entscheidbare Probleme and the Menge entshceidbare Probleme können durch die "Algorithmen" die diese lösen beshrieben werden und diese können durch wörter kodiert werder und die Menge alle Wörter ist abzählbar.
	- es gibt höchstens so viele Sprachen in P wie es Turing Machinen gibt
- for Verifikator & Zertifikat: it is not enough that there is a TM given an input and a Zert verifikator needs to work for all words

## random myhill nerode bit
| Concept                              | Meaning                                       |
| ------------------------------------ | --------------------------------------------- |
| LL has finite index under ≡L\equiv_L | ⇒ LL is regular                               |
| LL is regular                        | ⇒ There are finitely many ≡L\equiv_L-classes  |
| Each class = a unique "state"        | ⇒ Minimal DFA has exactly one state per class |
>[!quote] A myhill-Nerode class is the set of words that lrad to the same **"distinguishable behavior"** in the language

included in this definition is that words in the same Myhill-Nerode class will lead to 2 equivalent states in a DFA or to 1 state in a Minimal DFA

- you cannot simply complement an NFA by switching EZ with non-EZ (consider the example of an NFA that only accepts "a" but a can go to 2 zustände one EZ one not. this NFA would not change if they were flipped)
>[!warning] **Pumping lemma** is only applicable in the case of infinite languages (reg or kont frei) because pumping lemma generate unendlich viele Wörter

> be careful that all the conditions for something like the pumping lemma exist. can be a trick that a condition is missing so allows for flexibilty

> when we have the number of Zstnde = number residuale Sprachen that is including the extra "trap Zstd" that all non valid transitions go to (Fangzustand i think it's called)

> switching end and nicht-end Zustände on a minimal DFA gives you the minimal DFA for the complement

> Wörter der Länge n von Grammatiken in CNF haben sytax Bäume mit 3n-1 Knoten

>[!danger] **revise those questions about berechenbarkeit** "*we just need to say that the algo exists we don't need to give it"

> Both P and np are abzählbar

>[!danger] **In TMs deterministic or not, a halting state (accepting or refusing) is a state with no outgoing transitions**

>[!danger] **Strukturelle induktion reminder**

