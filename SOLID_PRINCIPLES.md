# SOLID Principles Implementation Reference

## Change Log

### : MovementUtil Refactoring
- Fixed: Removed calls to undefined `neighbour()` method in MovementUtil
- Changed: Replaced with direct calls to `Position.move(Direction[] step)`
- Before:
```java
for(Direction d: step) {
    current = current.neighbour(d);
}
return current;
```
- After:
```java
return current.move(step);
```
- Impact: Simplified movement logic and fixed compilation error while maintaining functionality

## Single Responsibility Principle (SRP)
Search for: `[SRP]`
- MoveValidator.java (Class level) - Lines 11-16: Class dedicated to move validation only
- MoveValidator.java (Method level) - Line 22: isValidMove method
- MoveValidator.java (Method level) - Line 35: isCorrectTurn method
- MoveValidator.java (Method level) - Line 43: wouldResultInCheck method
- MoveValidator.java (Method level) - Line 52: validateMove method combines validations
- MovementUtil.java - Each method handles a single aspect of movement calculation

## Open/Closed Principle (OCP)
Search for: `[OCP]`
- MoveValidator.java - Line 23: Move validation can be extended
- MoveValidator.java - Line 53: New validation rules can be added without modification
- MovementUtil.java - Movement calculation is open for extension through new Direction types but closed for modification

## Liskov Substitution Principle (LSP)
Search for: `[LSP]`
- MoveValidator.java - Line 54: Works with any Board implementation
- BasePiece.java - Entire class ensures substitutability of chess pieces
- MovementUtil.java - Works with any Position implementation that follows the move contract

LSP Violation Example:
- IGameInterface.getBoard() violates LSP by:
  1. Method name suggests returning board data but returns full GameState
  2. Behavioral contract doesn't match method name
  3. Client code expecting board data might break with substitute implementations
  4. GameController shows confusion with duplicate endpoints using same method

Fix: Either rename to getGameState() or split interface:
```java
public interface IBoardView {
    Map<String, String> getBoard();  // Just board data
}

public interface IGameState extends IBoardView {
    GameState getFullGameState();  // Complete state
}
```

## Interface Segregation Principle (ISP)
Search for: `[ISP]`
- MoveValidator.java - Line 36: Method uses minimal required interfaces
- MoveStrategy.java - Interface defines single responsibility for movement calculation
- MovementUtil.java - Methods require only the minimal interfaces needed for movement

## Dependency Inversion Principle (DIP)
Search for: `[DIP]`
- MoveValidator.java - Line 44: Depends on abstractions (Board interface)
- LinearMoveStrategy.java - Depends on BasePiece abstraction
- MovementUtil.java - Depends on Position and Direction abstractions, not concrete implementations

## Key Implementation Notes:
1. Each class has a single, well-defined responsibility
2. Validation rules can be extended without modifying existing code
3. All piece types can be substituted for BasePiece
4. Interfaces are specific to client needs
5. High-level modules depend on abstractions

## Recent Refactoring:
MovementUtil.java was refactored to:
1. Remove dependency on non-existent neighbour method
2. Use Position.move() method directly, following DIP
3. Maintain single responsibility for movement calculation
4. Keep method signatures stable while changing implementation
5. Preserve null-safety through stepOrNull methods

## Quick Search Guide:
- For SRP examples: Search for `[SRP]`
- For OCP examples: Search for `[OCP]`
- For LSP examples: Search for `[LSP]`
- For ISP examples: Search for `[ISP]`
- For DIP examples: Search for `[DIP]`



Current Composition Examples:
Board class uses composition with:
Map<Position, BasePiece> boardMap
Set<Position> highlightPolygons
Map<Colour, List<BasePiece>> eliminatedPieces

CompositionPiece class (which we created earlier) uses composition with:
PieceMovementHandler for movement logic
String symbol for piece representation
int value for piece value