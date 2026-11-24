// Project made by: Nizar EL IDRYSY, Mohammed LAMRANI HNINI, Hidaya SIMILLA, 4IIRG3, Paddle Pong Mini Game

package com.example.paddlepong

import android.os.Bundle
import androidx.compose.foundation.layout.fillMaxWidth
import androidx.compose.ui.text.style.TextAlign
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.Row
import androidx.compose.foundation.layout.Spacer
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.height
import androidx.compose.foundation.layout.padding
import androidx.compose.foundation.layout.size
import androidx.compose.foundation.layout.width
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material3.AlertDialog
import androidx.compose.material3.Button
import androidx.compose.material3.Checkbox
import androidx.compose.material3.OutlinedTextField
import androidx.compose.material3.OutlinedTextFieldDefaults
import androidx.compose.material3.Switch
import androidx.compose.material3.Text
import androidx.compose.material3.TextButton
import androidx.compose.runtime.Composable
import androidx.compose.runtime.LaunchedEffect
import androidx.compose.runtime.getValue
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
import androidx.compose.runtime.setValue
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextDecoration
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import kotlinx.coroutines.delay

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            PaddlePongApp()
        }
    }
}

@Preview
@Composable
fun PaddlePongApp() {
    // Reference dimensions and initial positions from the CdC
    val fieldWidth = 280
    val fieldHeight = 300

    val ballSize = 24
    val initialBallX = 128
    val initialBallY = 20

    val paddleWidth = 72
    val paddleHeight = 12
    val initialPaddleX = 104
    val paddleY = 268
    val paddleStep = 16

    // Setup and screen states
    var playerName by remember { mutableStateOf("Nizar") }
    var nameError by remember { mutableStateOf(false) }
    var selectedSpeed by remember { mutableStateOf("Normal 🚶") }
    var isDarkMode by remember { mutableStateOf(false) }
    var isPlaying by remember { mutableStateOf(false) }

    // Secret 999 score boost state on the header ball icon
    var scoreBoosted by remember { mutableStateOf(false) }
    var boostMessage by remember { mutableStateOf("") }

    // Game states
    var score by remember { mutableStateOf(0) }
    var isGameOver by remember { mutableStateOf(false) }
    var paddleX by remember { mutableStateOf(initialPaddleX) }
    var ballX by remember { mutableStateOf(initialBallX) }
    var ballY by remember { mutableStateOf(initialBallY) }
    var dx by remember { mutableStateOf(4) }
    var dy by remember { mutableStateOf(4) }

    // Colors controlled with if and Color (emojis only for icons)
    val bgColor = if (isDarkMode) Color(0xFF141118) else Color(0xFFF7F5FA)
    val textColor = if (isDarkMode) Color.White else Color.Black
    val fieldColor = if (isDarkMode) Color(0xFF1E2D1F) else Color(0xFF2E7D32)

    // Delay in ms according to selected speed
    val delayMs = when (selectedSpeed) {
        "Slow 🐌" -> 65L
        "Fast 🐆💨" -> 30L
        else -> 45L // "Normal 🚶"
    }

    // Reset ball and paddle to their starting positions
    fun resetPositions() {
        ballX = initialBallX
        ballY = initialBallY
        dx = 4
        dy = 4
        paddleX = initialPaddleX
    }

    // Clear the secret score boost and its message
    fun clearBoost() {
        scoreBoosted = false
        boostMessage = ""
    }

    // Ball movement loop while playing and not game over
    LaunchedEffect(isPlaying, isGameOver, delayMs) {
        while (isPlaying && !isGameOver) {
            delay(delayMs)

            // Move ball horizontally and vertically
            var nextX = ballX + dx
            var nextY = ballY + dy

            // Left and right wall bounces (max X on right = 280 - 24 = 256)
            if (nextX <= 0) {
                nextX = 0
                dx = -dx
            } else if (nextX >= fieldWidth - ballSize) {
                nextX = fieldWidth - ballSize
                dx = -dx
            }

            // Top wall bounce
            if (nextY <= 0) {
                nextY = 0
                dy = -dy
            }

            // Paddle collision when ball is moving down
            val crossesPaddle =
                dy > 0 && (nextY + ballSize) >= paddleY && ballY + ballSize <= paddleY + paddleHeight + 10
            val overlapsPaddle = (nextX + ballSize) > paddleX && nextX < (paddleX + paddleWidth)

            if (crossesPaddle && overlapsPaddle) {
                nextY = paddleY - ballSize // Place ball right above the paddle
                dy = -dy // Bounce upward
                score += 1 // Add 1 point
            }

            // Check if ball exited the bottom of the field
            if (nextY > fieldHeight) {
                resetPositions()
                clearBoost()
                isGameOver = true
            } else {
                ballX = nextX
                ballY = nextY
            }
        }
    }

    Column(
        modifier = Modifier
            .fillMaxSize()
            .background(bgColor)
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        // Top header with clickable icon, title, and dark mode Switch
        Row(
            verticalAlignment = Alignment.CenterVertically,
            horizontalArrangement = Arrangement.spacedBy(8.dp)
        ) {
            Text(
                text = "🎾",
                fontSize = 22.sp,
                modifier = Modifier.clickable {
                    score = 999
                    scoreBoosted = true
                    boostMessage = "Cheat mode activated! Score set to 999!"
                }
            )
            Text(
                text = "Paddle Pong Mini Game   ",
                fontSize = 16.sp,
                fontWeight = FontWeight.Bold,
                color = textColor
            )
            Switch(
                checked = isDarkMode,
                onCheckedChange = { isDarkMode = it }
            )
        }

        // Message shown when the secret score boost is triggered
        if (boostMessage.isNotEmpty()) {
            Text(
                text = boostMessage,
                fontSize = 12.sp,
                color = textColor,
                modifier = Modifier.padding(top = 4.dp)
            )
        }

        Spacer(modifier = Modifier.height(16.dp))

        if (!isPlaying) {
            // 1. Setup screen (scrollable): player name, speed checkboxes, Start button, and credits
            Column(
                modifier = Modifier
                    .weight(1f)
                    .verticalScroll(rememberScrollState()),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                OutlinedTextField(
                    value = playerName,
                    onValueChange = {
                        playerName = it
                        nameError = false
                    },
                    label = { Text("Enter your nametag") },
                    singleLine = true,
                    colors = OutlinedTextFieldDefaults.colors(
                        focusedTextColor = textColor,
                        unfocusedTextColor = textColor,
                        focusedLabelColor = textColor,
                        unfocusedLabelColor = textColor,
                        focusedBorderColor = textColor,
                        unfocusedBorderColor = textColor,
                        cursorColor = textColor
                    )
                )

                if (nameError) {
                    Text(
                        text = "Invalid nametag.",
                        color = Color.Red,
                        modifier = Modifier.padding(top = 4.dp)
                    )
                }

                Spacer(modifier = Modifier.height(16.dp))

                Text(
                    "Ball Speed",
                    fontWeight = FontWeight.Bold,
                    fontSize = 15.sp,
                    color = textColor
                )

                // Three exclusive speed checkboxes
                listOf("Slow", "Normal", "Fast").forEach { speed ->
                    Row(
                        verticalAlignment = Alignment.CenterVertically
                    ) {
                        Checkbox(
                            checked = selectedSpeed == speed,
                            onCheckedChange = { checked ->
                                if (checked) selectedSpeed = speed
                            }
                        )
                        Text(text = speed, color = textColor)
                    }
                }

                Spacer(modifier = Modifier.height(16.dp))

                Button(
                    onClick = {
                        if (playerName.isBlank()) {
                            nameError = true
                        } else {
                            nameError = false
                            if (!scoreBoosted) {
                                score = 0
                            }
                            isGameOver = false
                            resetPositions()
                            isPlaying = true
                        }
                    }
                ) {
                    Text("Start Paddling!")
                }

                Spacer(modifier = Modifier.height(24.dp))

                // Credits switching with dark mode
                Text(
                    text = " Credits - Paddle Pong Project",
                    fontWeight = FontWeight.Bold,
                    fontSize = 15.sp,
                    color = textColor
                )
                Spacer(modifier = Modifier.height(25.dp))
                Text(
                    text = "Developers",
                    fontWeight = FontWeight.Bold,
                    textDecoration = TextDecoration.Underline,
                    fontSize = 15.sp,
                    color = textColor
                )
                Spacer(modifier = Modifier.height(26.dp))
                Text(
                    text = "EL IDRYSY Nizar\nLAMRANI HNINI Mohammed\nSIMILLA Hidaya\n- 4IIRG3 EMSI-T -",
                    fontSize = 15.sp,
                    color = textColor,
                    textAlign = TextAlign.Center,
                    modifier = Modifier.fillMaxWidth()
                )
                Spacer(modifier = Modifier.height(27.dp))
                Text(
                    text = "Mentors",
                    fontWeight = FontWeight.Bold,
                    textDecoration = TextDecoration.Underline,
                    fontSize = 15.sp,
                    color = textColor
                )
                Spacer(modifier = Modifier.height(28.dp))
                Text(
                    text = "Mr. BENTAYEB Youness",
                    fontSize = 15.sp,
                    color = textColor
                )
                Spacer(modifier = Modifier.height(29.dp))
                Text(
                    text = "Date of Creation",
                    fontWeight = FontWeight.Bold,
                    textDecoration = TextDecoration.Underline,
                    fontSize = 15.sp,
                    color = textColor
                )
                Spacer(modifier = Modifier.height(30.dp))
                Text(
                    text = "21 November 2026",
                    fontSize = 15.sp,
                    color = textColor
                )
            }
        } else {
            // 2. Game screen: player info, score, field, paddle controls, and Back to Setup
            Text(
                text = "Player: $playerName · Mode: $selectedSpeed · Score: $score",
                color = textColor,
                fontWeight = FontWeight.Medium
            )

            Spacer(modifier = Modifier.height(12.dp))

            // Game field (280 x 300 dp)
            Box(
                modifier = Modifier
                    .size(width = fieldWidth.dp, height = fieldHeight.dp)
                    .background(fieldColor)
            ) {
                // Red rounded paddle (72 x 12 dp)
                Box(
                    modifier = Modifier
                        .padding(start = paddleX.dp, top = paddleY.dp)
                        .width(paddleWidth.dp)
                        .height(paddleHeight.dp)
                        .background(Color(0xFFE53935), RoundedCornerShape(5.dp))
                )

                // Ball (24 x 24 dp)
                Text(
                    text = "🥎",
                    fontSize = 18.sp,
                    modifier = Modifier
                        .padding(start = ballX.dp, top = ballY.dp)
                        .size(width = ballSize.dp, height = ballSize.dp)
                )
            }

            Spacer(modifier = Modifier.height(16.dp))

            // Left and Right paddle buttons
            Row(
                horizontalArrangement = Arrangement.spacedBy(12.dp)
            ) {
                Button(
                    onClick = {
                        val nextPaddleX = paddleX - paddleStep
                        paddleX = if (nextPaddleX < 0) 0 else nextPaddleX
                    }
                ) {
                    Text("<")
                }

                Button(
                    onClick = {
                        val maxPaddleX = fieldWidth - paddleWidth // 208 dp
                        val nextPaddleX = paddleX + paddleStep
                        paddleX = if (nextPaddleX > maxPaddleX) maxPaddleX else nextPaddleX
                    }
                ) {
                    Text(">")
                }
            }

            Spacer(modifier = Modifier.height(8.dp))

            TextButton(
                onClick = {
                    isGameOver = false
                    isPlaying = false
                    clearBoost()
                    score = 0
                    resetPositions()
                }
            ) {
                Text("Back to Setup", color = textColor)
            }
        }

        // Game Over dialog
        if (isGameOver) {
            AlertDialog(
                onDismissRequest = { },
                title = { Text("Game Over!") },
                text = { Text("$playerName, you scored $score.") },
                confirmButton = {
                    TextButton(
                        onClick = {
                            score = 0
                            clearBoost()
                            resetPositions()
                            isGameOver = false
                        }
                    ) {
                        Text("Play Again?")
                    }
                },
                dismissButton = {
                    TextButton(
                        onClick = {
                            isGameOver = false
                            isPlaying = false
                            clearBoost()
                            score = 0
                            resetPositions()
                        }
                    ) {
                        Text("End Game")
                    }
                }
            )
        }
    }
}