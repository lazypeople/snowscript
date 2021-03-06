About
+++++

Snowscript is a little language that compiles to PHP. It has its own syntax 
inspired by Python, Ruby, Go and Scala that strives to be DRY, clean and easy to 
read as well as write. For those familiar with Coffescript you could say that 
Snowscript is to PHP what Coffeescript is to Javascript.

Roadmap
+++++++

The current status as of May 13th, 2012 is that the basics work. There are
several things I want to fix before a first release candidate though:

Todo
====

- Webpage.
- Documentation.
- Named parameters.
- Advanced class syntax.
- List comprehension.
- Splats.
- Commandline compile tool.
- Loops and control structures as expressions.

Done
==== 

- For loops.
- Function style casts.
- Basic classes.
- Basic destructuring.
- Parsing of basic syntax.
- Transformations for the non LALR(1) compatible features of Snowscript like
  implicit parenthesis, the switch syntax and significant whitespace.
- Lexer.

Documentation
+++++++++++++

There is more thorough yet dated documentation on the google code page too.

Whitespace
==========

Snowscript has significant whitespace, meaning that the code structure is 
managed by indenting/dedenting and not by using brackets ({)}. Whitespace is not
significant inside comments, strings and brackets (() and []).

snowscript::

    fn find_person(person)
        <- access_third_eye(
            person.name,
            person.last_known_location
        )

php::

    function find_person($person) {
        return access_third_eye(
            $person->name,
            $person->last_known_location
        );
    }

Comments
========

snowscript::

    # Single line.
    # Single line as docblock #
    # This is a comment,

      spanning multiple lines.

php::

    // Single line.
    /**
     * Single line as docblock. 
     */
    /**
     * This is a comment,
     *
     * spanning multiple lines. 
     */

Arrays
======

Array are defined inside square brackets "[]". Items can be separated by "," or
by using whitespace. 

Keys are stringy when using "=" as a separator, and interpreted when using ":".

snowscript::

    pianists = ["McCoy Tyner", "Fred Hersch", "Bill Evans"]
    name = "Heroes"
    series = [
        name:
            genre = "Science Fiction"
            creator: "Tim Kring"
            seasons = 4
        Game Of Thrones =
            genre = "Medieval fantasy"
            creator = "David Benioff"
            seasons = 2
    ]


php::

    $pianists = array("McCoy Tyner", "Fred Hersch", "Bill Evans");
    $name = "Heroes";
    $series = array(
        $name => array(
            'genre' => "Science Fiction",
            'creator' => "Tim Kring",
            'seasons' => 4,
        ),
        "Game Of Thrones" => array(
            'genre' => "Medieval fantasy",
            'creator' => "David Benioff",
            'seasons' => 2,
        ),
    );

Strings
=======

There are four kind of strings: '"""', '"', "'''" and "'". Whitespace before the 
current indentation level is stripped. Strings can be concatenated using the "%"
operator.

snowscript::

    echo "I am" % " legend!";

php::

    echo "I am" . " legend!";

Quoted
------

Code inside "{}" adds their value to the string.

snowscript::

    fn travel
        echo "
        The {animal} went to {world.place()}
        with his {NUM} friends. 
        "

    """<a href="https://snowscript.org">Snowscript</a>\n"""


php::

    function travel() {
        echo "The " . $animal . " went to " . $world->place() . "\n"
        " with his " . NUM  . " friends.";
        
    }
    "<a href=\"https://snowscript.org\">Snowscript</a>\n";

Unquoted
--------

snowscript::

    'No {magic} here\n'
    '''{nor()} here.'''

php::

    'No {magic} here\n';
    '''{nor()} here.''';h

Functions
=========

The "fn" keyword is used to define functions, and "<-" to return a value.

Function calls can be chained using the "->" operator that passes the expression
before as the first argument to the next function.

snowscript::

    fn titlefy(FancyString fancystring)
        <- fancystring->trim(" -")->ucfirst()

php::

    function titlefy(FancyString $fancystring) {
        return ucfirst(trim($fancystring, " -"));
    }

Parameters
----------

Optional parameters must come after required parameters. They can be passed 
"null" to select the default value. This is helpful if you want to set a later
parameter to a non-default value.

Named parameters is supported using an array "[]" at the end of the function 
declaration. Named parameters with only a key are required, i.e. an exception
will be thrown if absent.

Optional and named parameters can not be used in the same function definition.

snowscript::

    fn render(template, format="html", [mood, color, allow_html=true, klingon=false])
        pass
    render("index.html", null, klingon=true, allow_html=false, mood="awesome", color="red")

php::

    function render($template, $format='html', $options_) {
        if ($format === null) {
            $format = 'html';
        }
        $defaults_ = array(
            'format' => "html", 
            'allow_html' => true, 
            'klingon' => false,
        );
        $options_ += $defaults_;
        $required_ = array('mood', 'color');
        foreach ($required_ as $key) {
            if (!isset($options_[$key])) {
                throw new InvalidArgumentException("$key is a required option.");
            }
        }
        unset($_key);
    }
    render("index.html", null, array('klingon'=>true, 'allow_html'=>false, 'mood'=>"awesome", 'color'=>"red"));

Destructuring
=============

snowscript::

    a, b, c = 1, 2, 3
    [a, b, [c, d]] = letters

php::

    list($a, $b, list($c, $d)) = $letters;

Splats
======

The splat operator "..." designates an unknown number of elements.

snowscript::

    fn decorate_many(content, ...)
        for style in ...
            content.decorate(style)
    decorate_many("Decorate this!", ...[Snowflakes(), Kittens(), Whiskers()])

    a, b, ... = get_letters()
    echo count(...)

php::

    function decorate_many($content) {
        $args_ = array_slice(func_get_args(), -1);
        foreach ($args_ as $style) {
            $content->decorate($style);
        }
    }
    $args_ = array(new Snowflakes, new Kittens, new Whiskers);
    array_unshift($args_, "Decorate this!");
    call_user_func_array("decorate_many", $args_);
    unset($args_);

    $tmp_ = get_letters();
    $splats_ = array_slice($_tmp, -1, count($_tmp) - 2);
    list($a, $b) = $tmp_; 
    echo count($splats_);

Control structures
==================

If
--

snowscript::

    if white_walkers.numbers < 500
        fight_valiantly()
    elif feeling_lucky
        improvise()
    else
        run()


php::

    if ($white_walkers->numbers < 500) {
        fight_valiantly();
    } elif ($feeling_lucky) { 
        improvise();
    } else {
        run();
    }

Switch
------

Stub.

snowscript::

    switch gamestate
        BESERKER
            signal("searchanddestroy")
        UNDERWATER
            gills.activate()
        NORMAL, default
            signal("playnice")
            gills.deactivate()


php::

    switch $gamestate {
        case BESERKER:
            signal("searchanddestroy");
            break;
        case UNDERWATER:
            gills.activate();
            break;
        case NORMAL:
        default:
            signal("playnice");
            gills.deactivate();
    }

Return
------

Both if and switch statements can be used as an expression.

snowscript::

    mood = if prince.is_in_the_house
        <- "Exquisite"
    else
        <- "Dull"


php::

     if ($prince->is_in_the_house) {
        $mood = "Exquisite";
    } else {
        $mood = "Dull";
    };

Loops
=====

For
---

Two kind of for loops are supported. Iterating over a collection, and iterating 
over a numeric range. Both key and value are local to the loop. An "&" can be 
used to designate the value as by-reference.

snowscript::

    for title, data in flowers
        echo "{data.id}: title"
    for &n in numbers
        n *= 2

    for i in 1 to 10 step 2
        echo i
    for i in 10 downto 1
        echo i

php::

    foreach ($flowers as $title => $data) {
        echo $data->id . ": " . $title;
    }
    unset($title, $data);
    foreach ($numbers as $n) {
        $n *= 2;
    }
    unset($n);

    for ($i=1, $i <= 10, $i+=2) {
        echo $i;
    }
    unset($i);
    for ($i=10, $i >= 0, --$i) {
        echo $i;
    }
    unset($i);

While
-----

snow::
    
    while frog.ass.is_watertight
        echo "Rinse and repeat."

php::

    while ($frog->ass->is_watertight) {
        echo "Rinse and repeat.";
    }

Array comprehension
===================

snowscript::

    [x, y for x in [1,2,3] for y in [3,1,4] if x != y]->var_dump
    
    fights = [[fight(samurai, villain)]
              for samurai in seven_samurais
                  if samurai->is_awake()
              for villain in seven_vaillains
                  if not villain->is_in_jail()
    ]

php::

    $result_ = array();
    foreach (array(1, 2, 3) as $x) {
        foreach (array(3, 1, 4) as $y) {
            if ($x != $y) {
                $result_[$x] = $y;
            }
        }
    }
    unset($x, $y);
    var_dump($result_);

    $fights = array();
    foreach ($seven_samurais as $samurai) {
        if (!$samurai->is_awake()) {
            continue;
        }
        foreach ($seven_villains as $villain) {
            if ($villain->is_in_jail()) {
                continue;
            }
            $fights[] = fight($samurai, $villain);
        }
    }
    unset($samurai, $villain);

Classes
=======

Declaration
-----------

The arguments to the class is given after the class name and the body of the
class works as the "constructor". 

The "." is used to access the class instance. "self" accesses the class.

snowscript::

    class TabularWriter(File path, title)
        # Properties. #
        title = title
        path = path
        self.filesystem = Filesystem().get()
        VERSION = 0.4
        # Here lie dragons.
        _filehandle = null

        # Constructor. #
        .check_filesystem()
        .init_file()

        # Methods. #
        fn check filesystem
            if self.filesystem not in supported_filesystems()
                throw UnsupportedFilesystemError()

        fn init_file
            if not file_exists(.path)
                throw FileMissingError()
            else
                ._filehandle = open_file(.path)

php::

    class TabularWriter {
        /**
         * Properties.
         */
        public $title;
        public $path;
        static $filesystem;
        const VERSION = 0.4;
        // Here lie dragons.
        public $_filehandle;

        /**
         * Constructor.
         */
        public function __construct(File path, title) {
            $this->title = $title;
            $this->path = $path;
            $filesystem_ = new Filesystem;
            self::$filesystem = $filesystem_.get();
            unset($filesystem_);
            $this->check_filesystem();
            $this->init_file();
        }

        /**
         * Methods.
         */
        public function check_filesystem() {
            $tmp_ = supported_filesystems();
            if (!isset($tmp_[self::$filesystem])) {
                throw new UnsupportedFilesystemError;
            }
            unset($tmp_);
        }

        public function init_file() {
            if (!file_exists($this->path)) {
                throw new FileMissingError;
            } else {
                $this->filehandle = open_file($this->path);
            }
        }
    }

Protected and private visibility is supported but not considered very "snowy", 
after all "We're all consenting adults here".

snowscript::

    class Boring
        private zzz
        protected hmm

php::

    class Boring {
        private $zzz;
        protected $hmm;        
    }

Stub.