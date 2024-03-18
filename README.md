# Regex to remove invisible characters.

>If trim doesn't remove the hidden characters, what can I use?

Consider using [RegEx](https://learn.microsoft.com/en-us/dotnet/api/system.text.regularexpressions.regex?view=net-8.0#definition) which would offer a highly customizable fallback option. Here are three basic extensions to try for starters, any of which work on the specific case you show.

```
/// <summary>
/// Basic examples.
/// </summary>
static partial class Extensions
{
    /// <summary>
    /// First pass. Very basic.
    /// </summary>
    public static string ToSafeCompare(this string input) =>
        Regex.Replace(input, "[^a-zA-Z0-9]", "");

    /// <summary>
    /// Use categories. Attempts to keep letters, marks, numbers, symbols, punctuation.
    /// </summary>
    public static string ToVisibleCharacters(this string input) =>
        Regex.Replace(input, "[^\\p{L}\\p{M}\\p{N}\\p{P}\\p{S}]", "");

    /// <summary>
    /// Remove control characters
    /// </summary>
    public static string ToNegateControlCharacters(this string input) =>
        Regex.Replace(input, "\\p{C}", "");

}
```
##### Console mock with the provided test bytes shown in the post.
```
using System.Text;
using System.Text.RegularExpressions;

Console.Title = "Try Regex";

string flash1 = Encoding.Unicode.GetString(new byte[]{ 70, 0, 108, 0, 97, 0, 115, 0, 104, 0 });
string flash2 = Encoding.Unicode.GetString(new byte[] { 70, 0, 108, 0, 97, 0, 115, 0, 104, 0, 11, 32 });

Console.WriteLine($"{flash1} {flash2}");
Console.WriteLine(string.Equals(flash1, flash2));
Console.WriteLine();

Console.WriteLine("Basic alphanumeric");
var test1A = flash1.ToSafeCompare();
var test1B = flash2.ToSafeCompare();
Console.WriteLine($"{test1A} {test1B}");
Console.WriteLine(string.Equals(test1A, test1B));
Console.WriteLine();

Console.WriteLine("Prelim filter using character categories");
var test2A = flash1.ToVisibleCharacters();
var test2B = flash2.ToVisibleCharacters();
Console.WriteLine($"{test2A} {test2B}");
Console.WriteLine(string.Equals(test2A, test2B));
Console.WriteLine();

Console.WriteLine("Prelim filter removes control characters");
var test3A = flash1.ToNegateControlCharacters();
var test3B = flash2.ToNegateControlCharacters();
Console.WriteLine($"{test3A} {test3B}");
Console.WriteLine(string.Equals(test3A, test3B));

Console.ReadKey();
```
[![console log][1]][1]


  [1]: https://i.stack.imgur.com/7muML.png