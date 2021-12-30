# Christmas puzzler 2021: scrambling text

### The puzzle

This repo contains two solutions to the Brandwatch holiday puzzler for 2021. I won't include the full details here, but essentially part 1 of the puzzler calls for rearranging the letters in each word of a text input, so that for example from the input

>It’s an example: The “2020-2021 Holiday Puzzler!”

we might generate the output

>Is’t an eaxmlpe: Teh “2002-2210 oHlidya Puzlzre!”

The rearrangement should:

- Swap letters within words but not between words
- Ignore and preserve punctuation and whitespace

This isn't very difficult so we will go off on some tangents (and take some shortcuts) to make it more exciting.

### Solution 1: Using a two-tape Turing machine (TM)

In theory, of course, one can implement any computable function with a [Turing machine](https://en.wikipedia.org/wiki/Turing_machine); but in practice, they're quite awkward to "program" with. But it turns out that for this particular problem, we can achieve a plausible-looking, albeit very simplistic, scrambling of the words using a two-tape TM with _only four states_.

The python notebook `TuringMachine/make_TM_spec.ipynb` produces a TM specification (also included as a text file `TuringMachine/turing_machine_spec.txt`) suitable for running at [turingmachinesimulator.com](https://turingmachinesimulator.com/), where you can see the execution animated. (If you run this, use only lowercase letters, enter spaces as underscores _, end your input with a fullstop and use no other punctuation.)

The idea for rearranging a word is:

1. work through the word, copying the letters at odd positions onto the second tape and replacing them with a special marker
2. "rewind" the first tape head back to the start of the word
3. work through the word again, replacing the special markers with letters from the second tape, now in the reverse order

The states, then, are:

- **State q0**: Working forwards through a word cutting out letters; at an odd position
- **State q1**: Working forwards through a word cutting out letters, at an even position
- **State q2**: Running the tape 1 head left to get back to the start of the word
- **State q3**: Working forward through the word, putting the stored letters back (in a different order)

Once you have these states, working out the transition rules needed for each state is fairly routine.

It's not hard to make the rearrangement of letters more complicated, but at the expense of additional machine states. Using such a small number of states also incurs some other limitations: it won't work if there is any punctuation other than a terminating full-stop, and leaves two-letter words unaltered. You can fix all that too, but at the expense of using more machine states.

The scrambling implemented is an _involution_: to unscramble the output (recover the original text) you just run it through the same Turing machine again.



### Solution 2: Finding the least visually similar rearrangements

If we wanted to rearrange the letters so that each rearranged word looked as little as possible like the original word, how should we do it? My idea was to permute the letters so that, on average, each permuted letter looks as visually dissimilar as possible from the letter that originally occupied that position in the word.

There are two ingredients that go into this.

- **A table of the visual similarity of each pair of letters.** I took this from a research paper called [A letter visual-similarity matrix for Latin-based alphabets](https://link.springer.com/article/10.3758/s13428-012-0271-4). According to the results of the paper, human judges consider the most similar letter pairs to be `il`, `bd`, `pq`, `hn` and  `vy`.
The least similar letter pairs were `jo`, `tw`, `qw`, `rw` and `ko`.

- **The [Hungarian algorithm](https://en.wikipedia.org/wiki/Hungarian_algorithm)**. The table of visual similarity gives us a way to score candidate rearrangements, but for longer words the number of permutations is huge and enumerating and scoring all of them becomes very slow.
But actually we have an instance of the [Assignment Problem](https://en.wikipedia.org/wiki/Assignment_problem), so we can solve it more efficiently using the Hungarian Algorithm; I used [this library](https://github.com/benchaplin/hungarian-algorithm).

I cut a bunch of corners here, and there's at least one bug somewhere, but you can get some results out of it.

aphyp ewn arye verobeyyd!




