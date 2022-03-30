# Creating Filler (7 Colors) in Rust

I am currently in the process of learning the Rust programming language, and I've gotten the urge to write an algorithm that can play Filler well, possibly better than a human opponent. But before I can do that, I need to have a way to play Filler games in Rust, and from what I've found, there aren't any libraries which provide a way to simulate Filler games.

If you haven't played Filler before, it's a two player game which is played on a square grid of boxes. The boxes are randomly colored, usually out of 7 colors. Each player starts on an opposite corner of the box, and can change the color of your box to another color. When you do, you capture all neighboring boxes which have the same color you chose. You keep switching colors, and you keep capturing more boxes which neighbor your captured boxes, until you control more than 50% of the boxes on the board. Once you reach this point, you have won.

![A game of Filler on GamePigeon](./images/gamepigeon-filler-example.webp)

My initial idea for the structure was have a `struct` which contained the current state of the game, including an array which would hold the actual grid. Then, outside code would use a constructor, then call functions to make players change into different colors. How outside code will obtain inputs doesn't matter.

The `struct` would also have have some functions to render the state. The implementation doesn't contain any actual UI, everything would be printed to the terminal. Making actual visual output for this project is out of my scope.

### Implementing the base game

To begin, let's make the actual `struct` we're going to be working with, as well as setting up some constants.

```rust
// The size of the grid, X and Y
const GRID_X: usize = 8;
const GRID_Y: usize = 7;
const GRID_SIZE: usize = GRID_X * GRID_Y;

// The number of colors we are going to have
const COLORS_NUM: usize = 7;

pub struct Filler {
    game_grid: [u8 ; GRID_SIZE]
}

impl Filler {
    pub fn new() -> Filler {
        return Filler {
            game_grid: [0 ; GRID_SIZE]
        };
    }
}
```

The `GRID_X` and `GRID_Y` will represent our X and Y height of the grid. The struct has one property so far, `game_grid`. This is an array which will represent the entire state of the board, going from the top left corner to the bottom right. The actual elements in this grid are the numbers which represent the current color of each box. For example, since the number of colors in ths example is 7, the possibilities for each element is 0-6.

For now, this code generates a game grid which is all 0s, but what we want it to do generate a random grid. Lets implement this.

```rust
use rand::{thread_rng, Rng};

// *snip*

impl Filler {
    pub fn new() -> Filler {
        return Filler {
            game_grid: make_random_grid()
        };
    }
}

fn make_random_grid() -> [u8 ; GRID_SIZE] {
    let mut rng = thread_rng();
    let mut to_return: [u8 ; GRID_SIZE] = [0 ; GRID_SIZE];

    for num in 0..(GRID_SIZE- 1) {
        let r: u8 = rng.gen_range(0..COLORS_NUM) as u8;
        to_return[num] = r;
    };

    return to_return;
}
```

Great. Here we use the `rand` crate to get random numbers. the `make_random_grid` function makes a random grid of numbers, in this case, 0-6 using `gen_range`.

At this point, we have no good way to see if our code is actually doing what we want it to, so we are going to have to implement a way of rendering the board.

Lets begin this by simply printing out a long string of numbers representing the board. In the `impl Filler` lets define a new method:

```rust
    pub fn render(self) -> String {
        let mut to_return = String::new();

        for element in self.game_grid {
            to_return.push(element.to_string().chars().next().unwrap());
        };

        return to_return;
    }
```

This method goes through each element in the `game_grid` and appends the number to a returned `String`. It does this by converting the `u8`into a`String`, then into an `Interator` which contains all the characters in the `String`. It iterates once, getting 1 character, then appending it onto `to_return`, finally returning `to_return`. Phew, what a complicated way to cast an integer into a character.

Let's create a main method and test our code out:

```rust
fn main() {
    let game = Filler::new();
    println!("{}", game.render());
}
```

Running this, we'll see something like this:

```
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.00s
     Running `target/debug/filler`
45400323345433121242116602413625612066111331544360623500
```


