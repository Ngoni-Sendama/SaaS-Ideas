# Packages

## FIlament AskAI for gemini, mistral

have model and tocken customizations on hintActions

```php
   Forms\Components\TextInput::make('text')
                            ->required()
                            ->hintActions(
                                [
                                    Action::make('generateWithAI')
                                        ->label('Generate With AI')
                                        ->icon('heroicon-m-sparkles')
                                        ->form([
                                            Textarea::make('prompt')
                                                ->label('Enter your prompt')
                                                ->required(),
                                        ])
                                        ->action(function (Set $set, array $data) {
                                            $response = Http::withHeaders([
                                                'Authorization' => 'Bearer ' . env('MISTRAL_API_KEY'), // or hardcoded if you're just testing
                                                'Content-Type' => 'application/json',
                                                'Accept' => 'application/json',
                                            ])
                                                ->post('https://api.mistral.ai/v1/chat/completions', [
                                                    'model' => 'mistral-large-latest',
                                                    'messages' => [
                                                        ['role' => 'user', 'content' => $data['prompt']],
                                                    ],
                                                ]);

                                            $aiText = $response->json('choices.0.message.content') ?? 'AI response not available.';

                                            // Log both the prompt and the response
                                            Log::info('Mistral API Prompt:', ['prompt' => $data['prompt']]);
                                            Log::info('Mistral API Response:', ['response' => $aiText]);

                                            $set('text', $aiText);
                                        }),
                                    Action::make('generateWithGeminiAI')
                                        ->label('Generate With Gemini AI')
                                        ->icon('heroicon-m-sparkles')
                                        ->color('secondary')
                                        ->form([
                                            Textarea::make('prompt')
                                                ->label('Enter your prompt')
                                                ->required(),
                                        ])
                                        ->action(function (Set $set, array $data) {
                                            $prompt = $data['prompt'];

                                            $response = Http::withHeaders([
                                                'Content-Type' => 'application/json',
                                                'Accept' => 'application/json',
                                            ])->post(
                                                'https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=' . env('GEMINI_API_KEY'),
                                                [
                                                    'contents' => [
                                                        [
                                                            'parts' => [
                                                                ['text' => $prompt],
                                                            ],
                                                        ],
                                                    ],
                                                ]
                                            );

                                            $aiText = $response->json('candidates.0.content.parts.0.text') ?? 'AI response not available.';

                                            Log::info('Gemini Prompt:', ['prompt' => $prompt]);
                                            Log::info('Gemini Response:', ['response' => $aiText]);

                                            $set('text', $aiText);
                                        })
                                ]
                            )
                            ->columnSpanFull(),
```
