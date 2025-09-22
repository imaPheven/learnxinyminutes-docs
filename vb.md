
---
name: Visual Basic
contributors:
    - ["imaPheven", "https://github.com/imaPheven"]
filename: LearnVB.vb
---

``` vb
' Visual Basic .NET Port of core examples from LearnCSharp.cs
' Mirrors: basic syntax, types, control flow, data structures, classes, properties,
' delegates/events, LINQ, exceptions, Using, extension methods, enums with Flags,
' indexers (Default Property), and inheritance.
' Notes:
' - Some C#-specific features (records, pattern matching switch expressions, deconstruction,
'   local functions, ref returns, null-conditional ?. on value types) are adapted or annotated.
' - VB does not have C# 9 "record" types; we use an immutable Class example instead.
' - For brevity, namespaces for Entity Framework samples are omitted.

Imports System
Imports System.Collections.Generic
Imports System.Linq
Imports System.Runtime.CompilerServices
Imports System.IO
Imports System.Threading.Tasks

Namespace Learning.VB

    Module LearnVB
        Sub Syntax()
            ' Console output
            Console.WriteLine("Hello World")
            Console.WriteLine("Integer: " & 10 & " Double: " & 3.14 & " Boolean: " & True)

            Console.Write("Hello ")
            Console.Write("World")

            ' Types & Variables
            Dim fooSbyte As SByte = 100
            Dim fooByte As Byte = 100
            Dim fooShort As Short = 10000
            Dim fooUshort As UShort = 10000
            Dim fooInt As Integer = 1
            Dim fooUint As UInteger = 1UI
            Dim fooLong As Long = 100000L
            Dim fooUlong As ULong = 100000UL
            Dim fooDouble As Double = 123.4
            Dim fooFloat As Single = 234.5F
            Dim fooDecimal As Decimal = 150.3D
            Dim fooBoolean As Boolean = True
            Dim fooChar As Char = "A"c
            Dim fooString As String = """escape"" quotes and add " & vbLf & " (new lines) and " & vbTab & " (tabs)"
            Console.WriteLine(fooString)
            Dim charFromString As Char = fooString(1)

            ' Compare strings (culture-aware ignore case)
            Dim cmp As Integer = String.Compare(fooString, "x", True)

            ' Composite formatting / interpolation
            Dim fooFs As String = String.Format("Check Check, {0} {1}, {0} {1:0.0}", 1, 2)
            Dim fooDate As DateTime = DateTime.Now
            Console.WriteLine(fooDate.ToString("hh:mm, dd MMM yyyy"))

            ' Verbatim string equivalent (use normal literals + doubled quotes as needed)
            Dim path As String = "C:\Users\User\Desktop"
            Dim verbatimPath As String = "C:\Users\User\Desktop"
            Console.WriteLine(path = verbatimPath) ' True

            Dim bazString As String = "Here's some stuff" & vbCrLf & "on a new line! ""Wow!"", the masses cried"

            Const HoursWorkPerWeek As Integer = 9001

            ' Arrays
            Dim intArray(9) As Integer
            Dim y() As Integer = {9000, 1000, 1337}
            Console.WriteLine("intArray @ 0: " & intArray(0))
            intArray(1) = 1

            ' Lists
            Dim intList As New List(Of Integer)()
            Dim stringList As New List(Of String)()
            Dim z As New List(Of Integer) From {9000, 1000, 1337}
            intList.Add(1)
            Console.WriteLine("intList at 0: " & intList(0))

            ' Operators
            Dim i1 As Integer = 1, i2 As Integer = 2
            Console.WriteLine(i1 + i2 - i1 * 3 \ 7) ' integer division

            Console.WriteLine("11 Mod 3 = " & (11 Mod 3))

            Console.WriteLine("3 = 2? " & (3 = 2))
            Console.WriteLine("3 <> 2? " & (3 <> 2))
            Console.WriteLine("3 > 2? " & (3 > 2))
            Console.WriteLine("3 < 2? " & (3 < 2))
            Console.WriteLine("2 <= 2? " & (2 <= 2))
            Console.WriteLine("2 >= 2? " & (2 >= 2))

            Dim k As Integer = 0
            Console.WriteLine(k) : k += 1
            k += 1 : Console.WriteLine(k)
            Console.WriteLine(k) : k -= 1
            k -= 1 : Console.WriteLine(k)

            ' Control structures
            Dim j As Integer = 10
            If j = 10 Then
                Console.WriteLine("I get printed")
            ElseIf j > 10 Then
                Console.WriteLine("I don't")
            Else
                Console.WriteLine("I also don't")
            End If

            Dim toCompare As Integer = 17
            Dim isTrue As String = If(toCompare = 17, "True", "False")

            Dim fooWhile As Integer = 0
            While fooWhile < 100
                fooWhile += 1
            End While

            Dim fooDoWhile As Integer = 0
            Do
                fooDoWhile += 1
                If fooDoWhile = 50 Then Exit Do
            Loop While fooDoWhile < 100

            For fooFor As Integer = 0 To 9
                ' iterate 10 times
            Next

            For Each ch As Char In "Hello World".ToCharArray()
                ' iterate over chars
            Next

            Dim month As Integer = 3
            Dim monthString As String
            Select Case month
                Case 1 : monthString = "January"
                Case 2 : monthString = "February"
                Case 3 : monthString = "March"
                Case 6, 7, 8 : monthString = "Summer time!!"
                Case Else : monthString = "Some other month"
            End Select

            ' Conversions
            Dim tryInt As Integer
            If Integer.TryParse("123", tryInt) Then Console.WriteLine(tryInt)
            Dim s As String = tryInt.ToString()

            Dim x As Long = CLng(CInt(15D))
        End Sub

        ' Entry point
        Sub Main(args As String())
            OtherInterestingFeatures()
        End Sub

        ' Default method signatures & optional params
        Public Function MethodSignatures(maxCount As Integer, Optional count As Integer = 0, Optional another As Integer = 3, ParamArray otherParams As String()) As Integer
            Return -1
        End Function

        Public Sub MethodSignatures(ByRef maxCount As Integer, ByRef count As Integer)
            count = 15
        End Sub

        ' Generics
        Public Function SetDefault(Of TKey, TValue)(dict As IDictionary(Of TKey, TValue), key As TKey, defaultItem As TValue) As TValue
            Dim result As TValue = Nothing
            If Not dict.TryGetValue(key, result) Then
                dict(key) = defaultItem
                Return defaultItem
            End If
            Return result
        End Function

        Public Sub IterateAndPrint(Of T As IEnumerable(Of Integer))(toPrint As T)
            For Each item As Integer In toPrint
                Console.WriteLine(item.ToString())
            Next
        End Sub

        ' Iterators (Yield)
        Public Iterator Function YieldCounter(Optional limit As Integer = 10) As IEnumerable(Of Integer)
            For n = 0 To limit - 1
                Yield n
            Next
        End Function

        Public Sub PrintYieldCounterToConsole()
            For Each counter In YieldCounter()
                Console.WriteLine(counter)
            Next
        End Sub

        Public Iterator Function ManyYieldCounter() As IEnumerable(Of Integer)
            Yield 0 : Yield 1 : Yield 2 : Yield 3
        End Function

        Public Iterator Function YieldCounterWithBreak(Optional limit As Integer = 10) As IEnumerable(Of Integer)
            For n = 0 To limit - 1
                If n > limit \ 2 Then
                    Return
                End If
                Yield n
            Next
        End Function

        Public Sub OtherInterestingFeatures()
            MethodSignatures(3, 1, 3, "Some", "Extra", "Strings")
            Dim maxCount As Integer = 0, cnt As Integer
            MethodSignatures(maxCount, cnt)

            ' Extension methods (see Module Extensions below)
            Dim i As Integer = 3
            i.Print()

            ' Nullable value types
            Dim nullable As Integer? = Nothing
            Console.WriteLine("Nullable variable: " & If(nullable.HasValue, nullable.Value.ToString(), "Nothing"))
            Dim notNullable As Integer = If(nullable, 0)
            nullable.PrintNullable()

            ' Implicit typing: use type inference with Dim (still strongly typed)
            Dim magic = "magic is a string"
            ' magic = 9 ' would fail

            Dim phonebook As New Dictionary(Of String, String) From {{"Sarah", "212 555 5555"}}
            Console.WriteLine(SetDefault(phonebook, "Shaun", "No Phone"))
            Console.WriteLine(SetDefault(phonebook, "Sarah", "No Phone"))

            Dim square As Func(Of Integer, Integer) = Function(x) x * x
            Console.WriteLine(square(3))

            Try
                Dim funBike = PennyFarthing.CreateWithGears(6)
                Dim some As String = Nothing
                some.ToLower() ' will throw
            Catch ex As NotSupportedException
                Console.WriteLine("Not so much fun now!")
            Catch ex As Exception
                Throw New ApplicationException("It hit the fan", ex)
            Finally
                ' cleanup
            End Try

            ' Using (IDisposable)
            Using writer As New StreamWriter("log.txt")
                writer.WriteLine("Nothing suspicious here")
            End Using

            ' Parallel.ForEach
            Dim words As New List(Of String) From {"dog", "cat", "horse", "pony"}
            Parallel.ForEach(words, Sub(word) Console.WriteLine(word))

            ' LINQ
            Dim bikes As New List(Of Bicycle)()
            bikes.Sort(Function(b1, b2) b1.Wheels.CompareTo(b2.Wheels))
            Dim result = bikes.Where(Function(b) b.Wheels > 3).Where(Function(b) b.IsBroken AndAlso b.HasTassles).Select(Function(b) b.ToString())
            Dim sum = bikes.Sum(Function(b) b.Wheels)
            Dim bikeSummaries = bikes.Select(Function(b) New With {.Name = b.Name, .IsAwesome = (Not b.IsBroken) AndAlso b.HasTassles})
            For Each bs In bikeSummaries.Where(Function(b) b.IsAwesome)
                Console.WriteLine(bs.Name)
            Next
        End Sub
    End Module

    ' Extension methods
    Module Extensions
        <Extension>
        Public Sub Print(obj As Object)
            Console.WriteLine(obj.ToString())
        End Sub

        <Extension>
        Public Sub PrintNullable(Of T As Structure)(nullable As Nullable(Of T))
            If nullable.HasValue Then
                Console.WriteLine(nullable.Value.ToString())
            End If
        End Sub
    End Module

    ' Delegates and events
    Public Class DelegateTest
        Public Shared count As Integer = 0
        Public Shared Function Increment() As Integer
            count += 1
            Return count
        End Function

        Public Delegate Function IncrementDelegate() As Integer
        Public Shared Event MyEvent As IncrementDelegate

        Public Shared Sub Main2(args As String())
            Dim inc As New IncrementDelegate(AddressOf Increment)
            Console.WriteLine(inc())

            Dim composedInc As IncrementDelegate = inc
            composedInc = CType([Delegate].Combine(composedInc, inc), IncrementDelegate)
            composedInc = CType([Delegate].Combine(composedInc, inc), IncrementDelegate)
            Console.WriteLine(composedInc())

            AddHandler MyEvent, AddressOf Increment
            AddHandler MyEvent, AddressOf Increment
            If MyEventEvent IsNot Nothing Then
                Console.WriteLine(MyEventEvent.Invoke())
            End If
        End Sub

        ' Backing field for event invocation in VB (auto-generated name pattern <EventName>Event)
        Private Shared MyEventEvent As IncrementDelegate
        Public Custom Event MyEventCustom As IncrementDelegate
            AddHandler(value As IncrementDelegate)
                MyEventEvent = CType([Delegate].Combine(MyEventEvent, value), IncrementDelegate)
            End AddHandler
            RemoveHandler(value As IncrementDelegate)
                MyEventEvent = CType([Delegate].Remove(MyEventEvent, value), IncrementDelegate)
            End RemoveHandler
            RaiseEvent()
                If MyEventEvent IsNot Nothing Then
                    MyEventEvent.Invoke()
                End If
            End RaiseEvent
        End Event
    End Class

    ' Classes
    Public Class Bicycle
        ' Auto-properties and full properties
        Public Property Cadence As Integer
        Protected Overridable Property Gear As Integer
        Friend Property Wheels As Integer
        Private _speed As Integer
        Public Property Name As String

        Public ReadOnly Property LongName As String
            Get
                Return Name & " " & _speed & " speed"
            End Get
        End Property

        Public Enum BikeBrand
            AIST
            BMC
            Electra = 42
            Gitane
        End Enum

        <Flags>
        Public Enum BikeAccessories
            None = 0
            Bell = 1
            MudGuards = 2
            Racks = 4
            Lights = 8
            FullPackage = Bell Or MudGuards Or Racks Or Lights
        End Enum

        Public Property Accessories As BikeAccessories

        Public Shared Property BicyclesCreated As Integer
        Private ReadOnly _hasCardsInSpokes As Boolean = False

        Public Sub New()
            Me.Gear = 1
            Cadence = 50
            _speed = 5
            Name = "Bontrager"
            Brand = BikeBrand.AIST
            BicyclesCreated += 1
        End Sub

        Public Sub New(startCadence As Integer, startSpeed As Integer, startGear As Integer, name As String, hasCardsInSpokes As Boolean, brand As BikeBrand)
            Me.New()
            Gear = startGear
            Cadence = startCadence
            _speed = startSpeed
            Me.Name = name
            _hasCardsInSpokes = hasCardsInSpokes
            Me.Brand = brand
        End Sub

        Public Sub New(startCadence As Integer, startSpeed As Integer, brand As BikeBrand)
            Me.New(startCadence, startSpeed, 0, "big wheels", True, brand)
        End Sub

        Public Sub SpeedUp(Optional increment As Integer = 1)
            _speed += increment
        End Sub

        Public Sub SlowDown(Optional decrement As Integer = 1)
            _speed -= decrement
        End Sub

        Private _hasTassles As Boolean
        Public Property HasTassles As Boolean
            Get
                Return _hasTassles
            End Get
            Set(value As Boolean)
                _hasTassles = value
            End Set
        End Property

        Public Property IsBroken As Boolean
        Public Property FrameSize As Integer

        Private passengers() As String = {"chris", "phil", "darren", "regina"}

        Default Public Property Item(i As Integer) As String
            Get
                Return passengers(i)
            End Get
            Set(value As String)
                passengers(i) = value
            End Set
        End Property

        Public Property Brand As BikeBrand

        Public Overridable Function Info() As String
            Return "Gear: " & Gear & " Cadence: " & Cadence & " Speed: " & _speed & " Name: " & Name & " Cards in Spokes: " & If(_hasCardsInSpokes, "yes", "no") & vbCrLf & "------------------------------" & vbCrLf
        End Function

        Public Shared Function DidWeCreateEnoughBicycles() As Boolean
            Return BicyclesCreated > 9000
        End Function
    End Class

    ' Inheritance and overrides
    Public Class PennyFarthing
        Inherits Bicycle

        Public Sub New(startCadence As Integer, startSpeed As Integer)
            MyBase.New(startCadence, startSpeed, 0, "PennyFarthing", True, Bicycle.BikeBrand.Electra)
        End Sub

        Protected Overrides Property Gear As Integer
            Get
                Return 0
            End Get
            Set(value As Integer)
                Throw New InvalidOperationException("You can't change gears on a PennyFarthing")
            End Set
        End Property

        Public Shared Function CreateWithGears(gears As Integer) As PennyFarthing
            Dim penny = New PennyFarthing(1, 1)
            ' Next line will throw due to override
            penny.Gear = gears
            Return penny
        End Function

        Public Overrides Function Info() As String
            Dim result As String = "PennyFarthing bicycle "
            result &= MyBase.ToString()
            Return result
        End Function
    End Class

    ' Interfaces
    Public Interface IJumpable
        Sub Jump(meters As Integer)
    End Interface

    Public Interface IBreakable
        ReadOnly Property Broken As Boolean
    End Interface

    Public Class MountainBike
        Inherits Bicycle
        Implements IJumpable, IBreakable

        Private damage As Integer = 0

        Public Sub Jump(meters As Integer) Implements IJumpable.Jump
            damage += meters
        End Sub

        Public ReadOnly Property Broken As Boolean Implements IBreakable.Broken
            Get
                Return damage > 100
            End Get
        End Property
    End Class

End Namespace
```